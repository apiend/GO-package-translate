包地址：http://golang.org/pkg/database/sql/
```golang
Package sql provides a generic interface around SQL (or SQL-like) databases.
The sql package must be used in conjunction with a database driver. See http://golang.org/s/sqldrivers for a list of drivers.
提供通用的sql（或者类sql）数据库的接口
sql包的使用必须关联一个数据库引擎。看 http://golang.org/s/sqldrivers 引擎列表
```

Variables
```golang
  var ErrNoRows = errors.New("sql: no rows in result set")
    ErrNoRows is returned by Scan when QueryRow doesn't return a row.
    In such a case, QueryRow returns a placeholder *Row value that defers this error until a Scan.
     当QueryRow 不返回行返回。
     在这种情况下，QueryRow 返回一个占位符*Row 的值，直接这个错误遇到下一个Scan
    
  var ErrTxDone = errors.New("sql: Transaction has already been committed or rolled back")
```

func Register
```golang  
func Register(name string, driver driver.Driver)
  Register makes a database driver available by the provided name. 
  If Register is called twice with the same name or if driver is nil, it panics.
   根据名字选择数据库引擎。
   如果Register 调用两次同一个名称或者driver是nil， 会引发恐慌
```

type DB
```golang
  type DB struct {
        // contains filtered or unexported fields
  }
  
  DB is a database handle. It's safe for concurrent use by multiple goroutines.
  
  The sql package creates and frees connections automatically; it also maintains a free pool of idle connections. 
  If the database has a concept of per-connection state, such state can only be reliably observed within a transaction. 
  Once DB.Begin is called, the returned Tx is bound to a single connection. 
  Once Commit or Rollback is called on the transaction, that transaction's connection is returned to DB's idle connection pool. 
  The pool size can be controlled with SetMaxIdleConns.
  
  DB是一个数据库句柄。它是安全 多goroutines并发
  sql包创建  并自动释放连接;它也会维持一个空的 闲置的连接池。
   如果每个数据库连接状态都有概念，状态只能可靠的观察一个事务。
  DB.Begin 一调用，返回的Tx 被绑定到单一的连接。 
  一旦提交或者回滚事务， 事务 连接会返回DB 的空闲 连接池。
  SetMaxIdleConns 可以控制池的大小。
```

```golang
  func Open(driverName, dataSourceName string) (*DB, error)
    Open opens a database specified by its database driver name and a driver-specific data source name, 
    	usually consisting of at least a database name and connection information.
    Most users will open a database via a driver-specific connection helper function that returns a *DB. 
    No database drivers are included in the Go standard library. See http://golang.org/s/sqldrivers for a list of third-party drivers.
    Open may just validate its arguments without creating a connection to the database. To verify that the data source name is valid, call Ping.
    
     根据数据库引擎名称和数据库资源名称打开一个数据库连接。通常由最少一个数据库名称和连接信息。
     大部分使用者 将会通过指定的引擎 帮助函数打开连接 然后返回 一个 *DB。GO的标准库不包含任何的数据库引擎。查看 http://golang.org/s/sqldrivers 第三方引擎
    Open 也许只会验证参数不会创建 数据库连接。调用Ping 验证数据资源名字是否有效。
```

func (*DB) Begin
```golang
  func (db *DB) Begin() (*Tx, error)
    Begin starts a transaction. The isolation level is dependent on the driver.
     启动一个事务。隔离级别依赖 引擎。
```

func (*DB) Close
```golang    
  func (db *DB) Close() error
    Close closes the database, releasing any open resources.
     关闭数据库，释放所有打开的资源。
```

func (*DB) Driver    
```golang
  func (db *DB) Driver() driver.Driver
    Driver returns the database's underlying driver.
     返回数据库底层的引擎。
```
  
func (*DB) Exec
```golang  
  func (db *DB) Exec(query string, args ...interface{}) (Result, error)
    Exec executes a query without returning any rows. The args are for any placeholder parameters in the query.
     执行一个查询 不返回任何行。args 是查询里的任意占位符参数。
```

func (*DB) Ping  
```golang
  func (db *DB) Ping() error
    Ping verifies a connection to the database is still alive, establishing a connection if necessary.
    验证连接是否保持可用，如果需要的话建立一个连接。
```

func (*DB) Prepare
```golang  
  func (db *DB) Prepare(query string) (*Stmt, error)
    Prepare creates a prepared statement for later queries or executions. 
    Multiple queries or executions may be run concurrently from the returned statement.
     给最后的查询或者执行创建一个准备好的语句。
     多个查询或者执行也许会同时运行 返回的语句。
```

func (*DB) Query
```golang
  func (db *DB) Query(query string, args ...interface{}) (*Rows, error)
    Query executes a query that returns rows, typically a SELECT. 
    The args are for any placeholder parameters in the query.
     运行查询返回行，通常是一个查询。args 是 查询里的任意占位符。
```
```golang    
Code:
age := 27
rows, err := db.Query("SELECT name FROM users WHERE age=?", age)
if err != nil {
        log.Fatal(err)
}
for rows.Next() {
        var name string
        if err := rows.Scan(&name); err != nil {
                log.Fatal(err)
        }
        fmt.Printf("%s is %d\n", name, age)
}
if err := rows.Err(); err != nil {
        log.Fatal(err)
}
```

func (*DB) QueryRow
```golang
  func (db *DB) QueryRow(query string, args ...interface{}) *Row
    QueryRow  executes a query that is expected to return at most one row. 
    QueryRow always return a non-nil value. Errors are deferred until Row's Scan method is called.
    运行一个预期的查询最多返回一行。
    经常返回非nil值。错误延迟到Row的Scan方法被调用。
``` 

Code:   
```golang
    id := 123
    var username string
    err := db.QueryRow("SELECT username FROM users WHERE id=?", id).Scan(&username)
    switch {
    case err == sql.ErrNoRows:
            log.Printf("No user with that ID.")
    case err != nil:
            log.Fatal(err)
    default:
            fmt.Printf("Username is %s\n", username)
    }
```

func (*DB) SetMaxIdleConns
```golang
  func (db *DB) SetMaxIdleConns(n int)
    SetMaxIdleConns sets the maximum number of connections in the idle connection pool.
    If n <= 0, no idle connections are retained.
    
     设置 空闲 连接池的最大连接数
     如果n<=0 ,idle连接不保留
```

func (*DB) SetMaxOpenConns
```golang
func (db *DB) SetMaxOpenConns(n int)
	SetMaxOpenConns sets the maximum number of open connections to the database.
	If MaxIdleConns is greater than 0 and the new MaxOpenConns is less than MaxIdleConns, 
		then MaxIdleConns will be reduced to match the new MaxOpenConns limit
	If n <= 0, then there is no limit on the number of open connections. The default is 0 (unlimited).
	
	设置打开数据库链接的最大连接数。
	如果MaxIdleConns 大于0 新的MaxOpenConns 比MaxIdleConns 小，MaxIdleConns将会减少到新的MaxOpenConns数
	如果n<=0 打开的连接数没有限制.默认值是0(没有限制)
```
  
type NullBool
```golang
  type NullBool struct {
        Bool  bool
        Valid bool // Valid is true if Bool is not NULL
  }
  NullBool represents a bool that may be null. 
  NullBool implements the Scanner interface so it can be used as a scan destination, similar to NullString.
  代表 也许是空的池。
  NullBool实现了Scanner接口，所以可以被scan 使用，类似NullString。
```

```golang
  func (n *NullBool) Scan(value interface{}) error
    Scan implements the Scanner interface.
    实现Scanner接口 (注bufio包里)
```

```golang
  func (n NullBool) Value() (driver.Value, error)
    Value implements the driver Valuer interface.
    实现引擎接口
``` 

type NullFloat64
```golang
  type NullFloat64 struct {
        Float64 float64
        Valid   bool // Valid is true if Float64 is not NULL
  }
  NullFloat64 represents a float64 that may be null. 
  NullFloat64 implements the Scanner interface so it can be used as a scan destination, similar to NullString.
  代表一个也许是空的float64。实现了扫描接口，所以可以被当成scan 使用，类似NullString。
```

```golang
  func (n *NullFloat64) Scan(value interface{}) error
    Scan implements the Scanner interface.
    实现Scanner接口
```

```golang
  func (n NullFloat64) Value() (driver.Value, error)
    Value implements the driver Valuer interface.
     实现引擎接口
```
    
type NullInt64
```golang
  type NullInt64 struct {
        Int64 int64
        Valid bool // Valid is true if Int64 is not NULL
  }
  NullInt64 represents an int64 that may be null. 
  NullInt64 implements the Scanner interface so it can be used as a scan destination, similar to NullString.
  代表一个也许是空的int64。实现了Scanner接口，所以可以被scan 使用，类似NullString。
```

func (n *NullInt64) Scan
```golang 
  func (n *NullInt64) Scan(value interface{}) error
    Scan implements the Scanner interface.
    实现Scanner接口
```

func (n NullInt64) Value
```golang
  func (n NullInt64) Value() (driver.Value, error)
    Value implements the driver Valuer interface.
    实现引擎接口
```

  
type NullString
```golang
  type NullString struct {
        String string
        Valid  bool // Valid is true if String is not NULL
  }
    NullString represents a string that may be null. NullString implements the Scanner interface so it can be used as a scan destination:
    代表一个也许是空的string。实现了Scanner接口，所以可以被scan 使用
    
    var s NullString
    err := db.QueryRow("SELECT name FROM foo WHERE id=?", id).Scan(&s)
    ...
    if s.Valid {
       // use s.String
    } else {
       // NULL value
    }
```    

```golang
    func (ns *NullString) Scan(value interface{}) error
      Scan implements the Scanner interface.
      	实现Scanner接口
```
      
```golang      
    func (ns NullString) Value() (driver.Value, error)
      Value implements the driver Valuer interface
      	实现引擎接口
```

       
type RawBytes
```golang
  RawBytes is a byte slice that holds a reference to memory owned by the database itself. 
  After a Scan into a RawBytes, the slice is only valid until the next call to Next, Scan, or Close.
  是一个持有数据库本身所拥有的内存的字节slice。当一个Scan 进入RawBytes后，slice都有效直到下一次调用 Next 、 Scan 或者 Close
```
  
type Result
```golang
	type Result interface {
	    // LastInsertId returns the integer generated by the database  相应一个命令返回数据库生成的整型，
	    // in response to a command. Typically this will be from an    通常情况下，这是来自插入新行自增的
	    // "auto increment" column when inserting a new row. Not all   不是所有的数据库都支持,这种语句的语法各不相同
	    // databases support this feature, and the syntax of such      
	    // statements varies.
	    LastInsertId() (int64, error)
	
	    // RowsAffected returns the number of rows affected by an    返回update, insert, or delete 受影响的行数 
	    // update, insert, or delete. Not every database or database
	    // driver may support this.
	    RowsAffected() (int64, error)
	}
	
	  A Result summarizes an executed SQL command.
  	   总结执行的SQL命令
```

type Row
```golang
  type Row struct {
        // contains filtered or unexported fields
  }
  Row is the result of calling QueryRow to select a single row.
  返回 调用QueryRow查询单行的结果
```

```golang
    func (r *Row) Scan(dest ...interface{}) error
      Scan copies the columns from the matched row into the values pointed at by dest. 
      If more than one row matches the query, Scan uses the first row and discards the rest. 
      If no row matches the query, Scan returns ErrNoRows.
      	复制匹配的行 到dest的值。如果超过一行匹配，Scan使用第一行丢弃其余的。如果没有匹配的，Scan 返回ErrNoRows
```
      
type Rows
```golang
  type Rows struct {
        // contains filtered or unexported fields
  }
  Rows is the result of a query. Its cursor starts before the first row of the result set. Use Next to advance through the rows:
  Rows是一个查询的结果。它的游标是从第一行之前开始的。使用Next 推进下一行：
  
  rows, err := db.Query("SELECT ...")
  ...
  for rows.Next() {
      var id int
      var name string
      err = rows.Scan(&id, &name)
      ...
  }
  err = rows.Err() // get any error encountered during iteration
  ...
```

func (rs *Rows) Close
```golang
  func (rs *Rows) Close() error
    Close closes the Rows, preventing further enumeration. 
    If the end is encountered, the Rows are closed automatically. Close is idempotent.
     关闭Rows，防止进一步列举。如果遇到结束，Rows会自动关闭。
```

func (*Rows) Columns
```golang    
  func (rs *Rows) Columns() ([]string, error)
    Columns returns the column names. 
    Columns returns an error if the rows are closed, or if the rows are from QueryRow and there was a deferred error.
      返回列的名字。如果rows关闭会返回错误，或者如果rows 从QueryRow  有延迟错误 会返回错误。
```

func (*Rows) Err
```golang      
  func (rs *Rows) Err() error
    Err returns the error, if any, that was encountered during iteration.
    返回在迭代中遇到的任何错误。
```

func (*Rows) Next
```golang   
  func (rs *Rows) Next() bool
    Next prepares the next result row for reading with the Scan method. 
    It returns true on success, false if there is no next result row. 
    Every call to Scan, even the first one, must be preceded by a call to Next.
     从读取Scan方法准备下一次的行结果。
     如果成功返回true，如果下一行的结果不存在返回false。
     每个调用Scan，即使是第一位，必须先调用Next。
```

func (*Rows) Scan
```golang
  func (rs *Rows) Scan(dest ...interface{}) error
    Scan copies the columns in the current row into the values pointed at by dest.
    If an argument has type *[]byte, Scan saves in that argument a copy of the corresponding data. 
    	The copy is owned by the caller and can be modified and held indefinitely. 
    	The copy can be avoided by using an argument of type *RawBytes instead; 
    	see the documentation for RawBytes for restrictions on its use.
    If an argument has type *interface{}, Scan copies the value provided by the underlying driver without conversion. 
    	If the value is of type []byte, a copy is made and the caller owns the result.
    
    复制当前的行到dest
    如果参数有type *[]byte，Scan 参数保存保存相应的复制数据。
    	这个复制的数据的拥有着是调用者，可以被修改并且是无期限的。这个拷贝可以使用 type *RawBytes 参数代替。查看文档的RawBytes限制使用。
    如果参数有type *interface{}，如果底层的引擎没有转换 Scan复制值。如果 值是type []byte，会复制并且调用者拥有返回值。
```

  
type Scanner
```golang
  type Scanner interface {
        // Scan assigns a value from a database driver.
        //
        // The src value will be of one of the following restricted
        // set of types:
        //
        //    int64
        //    float64
        //    bool
        //    []byte
        //    string
        //    time.Time
        //    nil - for NULL values
        //
        // An error should be returned if the value can not be stored
        // without loss of information.
        Scan(src interface{}) error
  }
  Scanner is an interface used by Scan.
  Scanner 是一个使用Scan的接口
```

type Stmt
```golang
  type Stmt struct {
        // contains filtered or unexported fields
  }
  Stmt is a prepared statement. Stmt is safe for concurrent use by multiple goroutines.
  是一个准备好的语句。安全多并发的goroutines。
```

func (*Stmt) Close
```golang
  func (s *Stmt) Close() error
    Close closes the statement.
     关闭语句
```

func (*Stmt) Exec
```golang  
  func (s *Stmt) Exec(args ...interface{}) (Result, error)
    Exec executes a prepared statement with the given arguments and returns a Result summarizing the effect of the statement.
    使用给定的参数执行一个准备好的语句，返回受影响的语句结果。
```

func (*Stmt) Query  
```golang
  func (s *Stmt) Query(args ...interface{}) (*Rows, error)
    Query executes a prepared query statement with the given arguments and returns the query results as a *Rows.
     使用给定的参数执行一个准备好的语句，返回查询的结果 *Rows。
```

func (*Stmt) QueryRow
```golang  
  func (s *Stmt) QueryRow(args ...interface{}) *Row
    QueryRow executes a prepared query statement with the given arguments. 
    If an error occurs during the execution of the statement, 
    that error will be returned by a call to Scan on the returned *Row, which is always non-nil.
    If the query selects no rows, the *Row's Scan will return ErrNoRows. 
    Otherwise, the *Row's Scan scans the first selected row and discards the rest.
     使用给定的参数执行一个准备好的语句。
     如果执行语句遇到错误，错误将会被返回给调用者的 *Row, 经常是非nile。
     如果查询没有行， *Row's Scan  将会返回 ErrNoRows。 其他的 *Row's Scan 会扫描 查询的第一行 并且其余的被丢弃。
    
    Example usage:
      var name string
      err := nameByUseridStmt.QueryRow(id).Scan(&name)
```  
  
type Tx
```golang
  type Tx struct {
        // contains filtered or unexported fields
  }
  Tx is an in-progress database transaction.
  A transaction must end with a call to Commit or Rollback.
  After a call to Commit or Rollback, all operations on the transaction fail with ErrTxDone.
  Tx是一个进程中的数据库事务。
   进程必须以提交或者回滚结束
   调用提交或者回滚后，所有的事务操作都会ErrTxDone失败
```

func (*Tx) Commit
```golang
  func (tx *Tx) Commit() error
    Commit commits the transaction.
     提交一个事务
```
func (*Tx) Exec
```golang
  func (tx *Tx) Exec(query string, args ...interface{}) (Result, error)
    Exec executes a query that doesn't return rows. For example: an INSERT and UPDATE.
     执行一个不返回行的查询，比如：一个插入或者更新。
```

func (*Tx) Prepare
```golang
  func (tx *Tx) Prepare(query string) (*Stmt, error)
    Prepare creates a prepared statement for use within a transaction.
    The returned statement operates within the transaction and can no longer be used once the transaction has been committed or rolled back.
    To use an existing prepared statement on this transaction, see Tx.Stmt.
     创建一个包含事务的准备好的语句。
     返回事务操作的语句， 不能比执行过的提交或者回滚长。
     查看Tx.Stmt 使用一个已经存在事务 准备好的语句
```

func (*Tx) Query
```golang  
  func (tx *Tx) Query(query string, args ...interface{}) (*Rows, error)
    Query executes a query that returns rows, typically a SELECT.
     执行一个查询返回行，通常是 SELECT。
```

func (*Tx) QueryRow
```golang  
  func (tx *Tx) QueryRow(query string, args ...interface{}) *Row
    QueryRow executes a query that is expected to return at most one row. 
    QueryRow always return a non-nil value. Errors are deferred until Row's Scan method is called.
     执行一个预期最少返回一行的查询。QueryRow 
     通常返回非nil值。错误延迟直到 Row's Scan 方法被调用。 
```    

func (tx *Tx) Rollback
```golang
  func (tx *Tx) Rollback() error
    Rollback aborts the transaction.
     回滚事务
```

func (*Tx) Stmt
```golang  
  func (tx *Tx) Stmt(stmt *Stmt) *Stmt
    Stmt returns a transaction-specific prepared statement from an existing statement.
     从一个存在的语句返回一个特定的事务准备语句
    
  Example:

  updateMoney, err := db.Prepare("UPDATE balance SET money=money+? WHERE id=?")
  ...
  tx, err := db.Begin()
  ...
  res, err := tx.Stmt(updateMoney).Exec(123.45, 98293203)  
```

