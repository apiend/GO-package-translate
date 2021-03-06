包地址：http://golang.org/pkg/compress/gzip/
Package gzip implements reading and writing of gzip format compressed files, as specified in RFC 1952.
包gzip实现在RFC 1952中规定的 gzip格式压缩文件的读写

```golang
Constants

const (
    NoCompression      = flate.NoCompression
    BestSpeed          = flate.BestSpeed
    BestCompression    = flate.BestCompression
    DefaultCompression = flate.DefaultCompression
)
These constants are copied from the flate package, so that code that imports "compress/gzip" does not also have to import "compress/flate".
这些constants是从flate包复制过来的，所以引入compress/gzip后 就不能引入compress/flate
```

```golang
Variables

var (
    // ErrChecksum is returned when reading GZIP data that has an invalid checksum.
    ErrChecksum = errors.New("gzip: invalid checksum")
    // ErrHeader is returned when reading GZIP data that has an invalid header.
    ErrHeader = errors.New("gzip: invalid header")
)
```

```golang
type Header
  type Header struct {
        Comment string    // comment
        Extra   []byte    // "extra data"
        ModTime time.Time // modification time
        Name    string    // file name
        OS      byte      // operating system type
  }
  The gzip file stores a header giving metadata about the compressed file. 
  That header is exposed as the fields of the Writer and Reader structs.
  gzip文件存储有关压缩文件的header的元数据。
  header公开读和写的结构体。
```

```golang 
type Reader
  type Reader struct {
        Header
        // contains filtered or unexported fields
  }
    A Reader is an io.Reader that can be read to retrieve uncompressed data from a gzip-format compressed file.
    Reader是一个可以从gzip形式的压缩文件取回未压缩数据的io.Reader
    In general, a gzip file can be a concatenation of gzip files, each with its own header.
    Reads from the Reader return the concatenation of the uncompressed data of each.
     Only the first header is recorded in the Reader fields.
     一般情况下，gzip文件可以是一个级联的gzip文件，每个都有它自己的头。Reads从Reader返回每个级联未压缩的数据。只有第一个header会在Reader字段记录。
    Gzip files store a length and checksum of the uncompressed data. 
    The Reader will return a ErrChecksum when Read reaches the end of the uncompressed data if it does not have the expected length or checksum. 
    Clients should treat data returned by Read as tentative until they receive the io.EOF marking the end of the data.
    Gzip文件存储长度和校验未压缩数据。如果Reader在读取完未压缩数据没有遇到预期的长度和校验会返回一个ErrChecksum
```

```golang    
    func NewReader(r io.Reader) (*Reader, error)
      NewReader creates a new Reader reading the given reader. 
      The implementation buffers input and may read more data than necessary from r. 
      It is the caller's responsibility to call Close on the Reader when done.
        创建一个给定读取的Reader。
        实现缓冲输入并且也许会从r中读取更多的数据。
        在调用者完成，调用Reader的Close关闭。
```

```golang    
    func (z *Reader) Close() error
      Close closes the Reader. It does not close the underlying io.Reader.
      关闭Reader。不会关闭底层的io.Reader。
```

```golang
    func (z *Reader) Read(p []byte) (n int, err error)
```
    
```golang   
type Writer
  type Writer struct {
        Header
        // contains filtered or unexported fields
  }
  A Writer is an io.WriteCloser that satisfies writes by compressing data written to its wrapped io.Writer.
  Writer是一个io.WriteCloser满足写入压缩数据
``` 

```golang
    func NewWriter(w io.Writer) *Writer
      NewWriter creates a new Writer that satisfies writes by compressing data written to w.
      创建一个新的Writer满足把压缩数据写入w
      It is the caller's responsibility to call Close on the WriteCloser when done. 
      Writes may be buffered and not flushed until Close.
      在调用者完成后关闭。Writes也许会缓冲但是直到关闭才会刷新。
      Callers that wish to set the fields in Writer.Header must do so before the first call to Write or Close. 
      The Comment and Name header fields are UTF-8 strings in Go, but the underlying format requires NUL-terminated ISO 8859-1 (Latin-1). 
      NUL or non-Latin-1 runes in those strings will lead to an error on Write.
        调用者如果希望设置Writer.Header，必须在第一次调用或者关闭之前设置。
        在GO里评论和名称的头是utf8字符串，但是底层的形式是以NUL结尾的 ISO 8859-1 (Latin-1)。
      NUL 或者 non-Latin-1 runes在写的时候会导致错误。
```

```golang    
    func NewWriterLevel(w io.Writer, level int) (*Writer, error)
      NewWriterLevel is like NewWriter but specifies the compression level instead of assuming DefaultCompression.
      NewWriterLevel和NewWriter一样但是有规定压缩的级别代替默认的级别
      The compression level can be DefaultCompression, NoCompression, or any integer value between BestSpeed and BestCompression inclusive. 
      The error returned will be nil if the level is valid.
      压缩级别 在最快速度和最好压缩之间 可以是默认的压缩，没有压缩或者任何整型类型的值。如果级别是无效的刚返回nil
```

```golang    
    func (z *Writer) Close() error
      Close closes the Writer. It does not close the underlying io.Writer.
      关闭Writer。不会关闭底层的io.Writer.
```

```golang    
    func (z *Writer) Flush() error
      Flush flushes any pending compressed data to the underlying writer.
      刷新任何底层追家的压缩数据writer
      It is useful mainly in compressed network protocols, to ensure that a remote reader has enough data to reconstruct a packet. Flush does not return until the data has been written. If the underlying writer returns an error, Flush returns that error.
      主要用在网络压缩协议，确保远程读取能有足够的数据重建成包。数据写完之前不会刷新。如果底层的writer返回错误，Flush会返回那个错误。
      In the terminology of the zlib library, Flush is equivalent to Z_SYNC_FLUSH.
      在zlib类型里，Flush相当于Z_SYNC_FLUSH
```    

```golang
    func (z *Writer) Write(p []byte) (int, error)
      Write writes a compressed form of p to the underlying io.Writer. The compressed bytes are not necessarily flushed until the Writer is closed.
      写入压缩形式的底层io.Writer.压缩字节直到Writer关闭后才刷新
```