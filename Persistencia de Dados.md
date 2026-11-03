# Relatório de Atividade Prática – Persistência de Dados com ORM e CRUD

**Aluno:** Pedro Augusto De Jesus Castro  
**Matrícula:** 202604061  

---

## 1. Introdução e Escolha da Classe Model
Para realizar esta atividade prática de Mapeamento Objeto-Relacional (ORM), escolhi a classe **`Paciente`**. Nela, estruturei os atributos essenciais para representar um paciente, como nome, CPF, idade (utilizada como chave primária/identificador), endereço, sexo e data de nascimento.

O mapeamento foi feito utilizando o **ORMLite** em conjunto com o **SQLite**, aplicando as anotações `@DatabaseTable` na classe e `@DatabaseField` nos atributos para que o framework criasse e gerenciasse a tabela automaticamente no banco de dados. Para organizar as operações de persistência, utilizei o padrão **Repository** através da classe `PacienteRepository`, isolando a lógica de banco de dados do restante da aplicação.

---

## 2. Passo a Passo da Implementação e Testes no BlueJ

Para testar o sistema, fiz todo o processo de forma interativa no **BlueJ**, seguindo esta rotina:
1. Instanciei a classe `Database` passando o nome do arquivo de banco de dados (`hospital.db`).
2. Instanciei a classe `PacienteRepository` passando essa conexão do banco. Nesse momento, a tabela `Paciente` foi criada no SQLite de forma automática.
3. Criei uma instância de `Paciente` (`paciente1`), preenchi os dados usando os métodos `set` (como `setnome`, `setCPF`, `setendereco`, `setsexo` e a idade como ID).
4. Chamei o método `create` do repositório para persistir os dados.

---

## 3. Comprovação das Operações CRUD (Com Prints)

### - Create (Cadastro)
Aqui realizei a criação e o salvamento do primeiro registro de paciente no banco de dados utilizando o repositório.

> **Print da Operação Create:**  
> ![Create no BlueJ](images/create.png)  
> *Figura 1: Instanciação do objeto paciente e chamada do método create para gravação no banco.*

---

### - Read (Consulta)
Para validar se os dados realmente foram salvos, utilizei os métodos de carregamento para buscar as informações diretamente do SQLite.

> **Print da Operação Read:**  
> ![Read no BlueJ](images/read.png)  
> *Figura 2: Consulta dos registros armazenados no banco de dados.*

---

### - Update (Atualização)
Após cadastrar, testei a alteração de dados. Modifiquei o nome do paciente utilizando o método `setnome` e chamei o método `update` do repositório. O ORMLite atualizou o registro com sucesso na tabela.

> **Print da Operação Update:**  
> ![Update no BlueJ](images/update.png)  
> *Figura 3: Modificação de dados do paciente e execução bem-sucedida do método update.*

---

### - Delete (Exclusão)
Por fim, testei a remoção do registro chamando o método `delete` do repositório passando o objeto correspondente. Uma consulta posterior confirmou que o registro foi excluído da base.

> **Print da Operação Delete:**  
> ![Delete no BlueJ](images/delete.png)  
> *Figura 4: Execução do método delete e confirmação da remoção do registro.*

---

## 4. Código-Fonte da Implementação

Abaixo estão os códigos das classes desenvolvidas para o projeto:

### Classe de Modelo (`Paciente.java`)
```java
import java.util.Date;
import java.text.SimpleDateFormat;
import com.j256.ormlite.table.DatabaseTable;
import com.j256.ormlite.field.DatabaseField;
import com.j256.ormlite.field.DataType;

@DatabaseTable(tableName = "Paciente")
public class Paciente {    
    @DatabaseField(generatedId = true)
    private int idade;
      
    @DatabaseField
    private String nome;
      
    @DatabaseField
    public String CPF;
      
    @DatabaseField(dataType=DataType.DATE)
    public Date birthday;  
      
    @DatabaseField
    public String endereco;
      
    @DatabaseField
    public String sexo;
      
    public String printBirthday() {
        SimpleDateFormat dateFor = new SimpleDateFormat("dd/MM/yyyy");
        return dateFor.format(birthday);
    }

    public int getidade(){
        return this.idade;
    }

    public void setId(int id){
        this.idade = id;
    }

    public String getnome(){
        return this.nome;
    }

    public void setnome(String nome){
        this.nome = nome;
    }

    public String getCPF(){
        return this.CPF;
    }

    public void setCPF(String CPF){
        this.CPF = CPF;
    }

    public Date getBirthday(){
        return this.birthday;
    }

    public void setBirthday(Date birthday){
        this.birthday = birthday;
    }
      
    public void setendereco(String endereco){
        this.endereco = endereco;
    }
      
    public String getendereco(){
        return this.endereco;
    }
      
    public void setsexo(String sexo){
        this.sexo = sexo;
    }
    
    public String getsexo(){
        return this.sexo;
    }
}
```

### Classe de Repositório (`PacienteRepository.java`)
```java
import com.j256.ormlite.dao.DaoManager;
import com.j256.ormlite.dao.Dao;
import java.sql.SQLException;
import com.j256.ormlite.table.TableUtils;
import java.util.List;
import java.util.ArrayList;

public class PacienteRepository {
    private static Database database;
    private static Dao<Paciente, Integer> dao;
    private List<Paciente> loadedPacientes;
    private Paciente loadedPaciente; 
      
    public PacienteRepository(Database database) {
        PacienteRepository.setDatabase(database);
        loadedPacientes = new ArrayList<Paciente>();
    }
      
    public static void setDatabase(Database database) {
        PacienteRepository.database = database;
        try {
            dao = DaoManager.createDao(database.getConnection(), Paciente.class);
            TableUtils.createTableIfNotExists(database.getConnection(), Paciente.class);
        }
        catch(SQLException e) {
            System.out.println(e);
        }        
    }
      
    public Paciente create(Paciente Paciente) {
        int nrows = 0;
        try {
            nrows = dao.create(Paciente);
            if ( nrows == 0 )
                throw new SQLException("Error: object not saved");
            this.loadedPaciente = Paciente;
            loadedPacientes.add(Paciente);
        } catch (SQLException e) {
            System.out.println(e);
        }
        return Paciente;
    }   

    public void update(Paciente paciente) {
        try {
            dao.update(paciente);
            this.loadedPaciente = paciente;
            System.out.println("Paciente atualizado com sucesso!");
        } catch (SQLException e) {
            System.out.println(e);
        }
    }

    public void delete(Paciente paciente) {
        try {
            dao.delete(paciente);
            this.loadedPaciente = null;
            System.out.println("Paciente excluído com sucesso!");
        } catch (SQLException e) {
            System.out.println(e);
        }
    }
      
    public Paciente loadFromId(int id) {
        try {
            this.loadedPaciente = dao.queryForId(id);
            if (this.loadedPaciente != null)
                this.loadedPacientes.add(this.loadedPaciente);
        } catch (SQLException e) {
            System.out.println(e);
        }
        return this.loadedPaciente;
    }   
      
    public List<Paciente> loadAll() {
        try {
            this.loadedPacientes =  dao.queryForAll();
            if (this.loadedPacientes.size() != 0)
                this.loadedPaciente = this.loadedPacientes.get(0);
        } catch (SQLException e) {
            System.out.println(e);
        }
        return this.loadedPacientes;
    }
}
```

### Classe de Banco de Dados (`Database.java`)
```java
import java.sql.*;
import com.j256.ormlite.jdbc.JdbcConnectionSource;

public class Database {
   private String databaseName = null;
   private JdbcConnectionSource connection = null;
   
   public Database(String databaseName) {
        this.databaseName = databaseName;
   }   
   
   public JdbcConnectionSource getConnection() throws SQLException {
      if ( databaseName == null ) {
          throw new SQLException("database name is null");
      }
      if ( connection == null ) {
          try {
              connection = new JdbcConnectionSource("jdbc:sqlite:"+databaseName);             
          } catch ( Exception e ) {
              System.err.println( e.getClass().getName() + ": " + e.getMessage() );
              System.exit(0);
          }
          System.out.println("Opened database successfully");
      }
      return connection;
   }
   
   public void close() {
       if ( connection != null ) {
           try {
               connection.close();
               this.connection = null;
           } catch (java.lang.Exception e) {
               System.err.println(e);
           }
       }
   }
}
```

---

## 5. Conclusão
Com essa prática, consegui entender na prática como funciona o mapeamento ORM utilizando Java, ORMLite e SQLite. A adoção do padrão Repository deixou o código bem organizado e modular, facilitando bastante a execução e o teste de todas as operações do CRUD de forma interativa.