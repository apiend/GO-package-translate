包地址：http://golang.org/pkg/debug/macho/
```golang
Package macho implements access to Mach-O object files.
macho包实现对Mach-O 对象文件的访问
```

Constants
```golang
  const (
        Magic32 uint32 = 0xfeedface
        Magic64 uint32 = 0xfeedfacf
  )
```

 
type Cpu
```golang 
  type Cpu uint32
  A Cpu is a Mach-O cpu type.
  是一个Mach-O cpu类型
  
  const (
          Cpu386   Cpu = 7
          CpuAmd64 Cpu = Cpu386 + 1<<24
  )

func (Cpu) GoString
```golang  
    func (i Cpu) GoString() string
```

func (Cpu) String
```golang    
    func (i Cpu) String() string
```    
```    

type Dylib
```golang
  type Dylib struct {
        LoadBytes
        Name           string
        Time           uint32
        CurrentVersion uint32
        CompatVersion  uint32
  }
  A Dylib represents a Mach-O load dynamic library command.
  表示一个载入动态命令库的Mach-O
```
  
type DylibCmd
```golang
  type DylibCmd struct {
        Cmd            LoadCmd
        Len            uint32
        Name           uint32
        Time           uint32
        CurrentVersion uint32
        CompatVersion  uint32
  }
  A DylibCmd is a Mach-O load dynamic library command.
  是一个载入动态命令库的Mach-O
```

type Dysymtab
```golang
  type Dysymtab struct {
        LoadBytes
        DysymtabCmd
        IndirectSyms []uint32 // indices into Symtab.Syms
  }
  A Dysymtab represents a Mach-O dynamic symbol table command.
  表示一个动态符号表命令的 Mach-O 
```
  
type DysymtabCmd
```golang
  type DysymtabCmd struct {
        Cmd            LoadCmd
        Len            uint32
        Ilocalsym      uint32
        Nlocalsym      uint32
        Iextdefsym     uint32
        Nextdefsym     uint32
        Iundefsym      uint32
        Nundefsym      uint32
        Tocoffset      uint32
        Ntoc           uint32
        Modtaboff      uint32
        Nmodtab        uint32
        Extrefsymoff   uint32
        Nextrefsyms    uint32
        Indirectsymoff uint32
        Nindirectsyms  uint32
        Extreloff      uint32
        Nextrel        uint32
        Locreloff      uint32
        Nlocrel        uint32
  }
  A DysymtabCmd is a Mach-O dynamic symbol table command.
  是一个动态符号表命令的 Mach-O 
```
  
type File
```golang
  type File struct {
        FileHeader
        ByteOrder binary.ByteOrder
        Loads     []Load
        Sections  []*Section

        Symtab   *Symtab
        Dysymtab *Dysymtab
        // contains filtered or unexported fields
  }
  A File represents an open Mach-O file.
  表示一个打开的Mach-O 文件
```

func NewFile
```golang
  func NewFile(r io.ReaderAt) (*File, error)
    NewFile creates a new File for accessing a Mach-O binary in an underlying reader. 
    The Mach-O binary is expected to start at position 0 in the ReaderAt.
     创建一个 新的 在底层的reader 接收二进制Mach-O File。在ReaderAt 中  Mach-O 二进制  预期从0位置开始。
```

func Open
```golang  
  func Open(name string) (*File, error)
    Open opens the named file using os.Open and prepares it for use as a Mach-O binary.
    使用os.Open 打开一个命名文件  并且 用 Mach-O 二进制 准备它。
```
  
func (*File) Close
```golang  
  func (f *File) Close() error
    Close closes the File. If the File was created using NewFile directly instead of Open, Close has no effect.
    关闭文件。如果文件使用NewFile 创建 用open替代，Close 没有影响
```

func (*File) DWARF
```golang    
  func (f *File) DWARF() (*dwarf.Data, error)
    DWARF returns the DWARF debug information for the Mach-O file.
    返回Mach-O 文件的DWARF debug 信息。
```

func (*File) ImportedLibraries
```golang    
  func (f *File) ImportedLibraries() ([]string, error)
    ImportedLibraries returns the paths of all libraries referred to by the binary f that are expected to be linked with the binary at dynamic link time.
     在动态链接时预期使用二进制链接，根据二进制f返回所有类库的路径。
```

func (*File) ImportedSymbols
```golang
  func (f *File) ImportedSymbols() ([]string, error)
    ImportedSymbols returns the names of all symbols referred to by the binary f that are expected to be satisfied by other libraries at dynamic load time.  
    在载入动态链接时预期使用二进制链接，根据二进制f返回所有类库的路径。
```

func (*File) Section
```golang
  func (f *File) Section(name string) *Section
    Section returns the first section with the given name, or nil if no such section exists.
    使用给定名字返回第一个节，如果节不存在 返回nil
```

func (*File) Segment
```golang  
  func (f *File) Segment(name string) *Segment
    Segment returns the first Segment with the given name, or nil if no such segment exists.
     使用给定的名称返回第一Segment，如果段不存在 返回nil
```

type FileHeader
```golang
  type FileHeader struct {
        Magic  uint32
        Cpu    Cpu
        SubCpu uint32
        Type   Type
        Ncmd   uint32
        Cmdsz  uint32
        Flags  uint32
  }
  A FileHeader represents a Mach-O file header.
  表示一个 Mach-O 文件头。
``` 

type FormatError
```golang
  type FormatError struct {
        // contains filtered or unexported fields
  }
  FormatError is returned by some operations if the data does not have the correct format for an object file.
  如果文件对象没有正确的格式，返回一些操作。
```

func (*FormatError) Error
```golang  
    func (e *FormatError) Error() string
```    
    
type Load
```golang
  type Load interface {
        Raw() []byte
  }
  A Load represents any Mach-O load command.
  表示任何载入命令的Mach-O 
```
  
type LoadBytes
```golang
  type LoadBytes []byte
    A LoadBytes is the uninterpreted bytes of a Mach-O load command.
    载入未解释字节的Mach-O .
```  

func (LoadBytes) Raw
```golang
    func (b LoadBytes) Raw() []byte
```  
    
type LoadCmd
```golang
  type LoadCmd uint32
    A LoadCmd is a Mach-O load command.
    载入Mach-O命令
    
  const (
          LoadCmdSegment    LoadCmd = 1
          LoadCmdSymtab     LoadCmd = 2
          LoadCmdThread     LoadCmd = 4
          LoadCmdUnixThread LoadCmd = 5 // thread+stack
          LoadCmdDysymtab   LoadCmd = 11
          LoadCmdDylib      LoadCmd = 12
          LoadCmdDylinker   LoadCmd = 15
          LoadCmdSegment64  LoadCmd = 25
  )
```

func (LoadCmd) GoString
```golang
    func (i LoadCmd) GoString() string
```

func (LoadCmd) String
```golang    
    func (i LoadCmd) String() string
```
    
type Nlist32
```golang
  type Nlist32 struct {
        Name  uint32
        Type  uint8
        Sect  uint8
        Desc  uint16
        Value uint32
  }
  An Nlist32 is a Mach-O 32-bit symbol table entry.
  是一个32位的输入符号表。
```

type Nlist64
```golang
  type Nlist64 struct {
        Name  uint32
        Type  uint8
        Sect  uint8
        Desc  uint16
        Value uint64
  }
  An Nlist64 is a Mach-O 64-bit symbol table entry.
  是一个64位的输入符号表。
```

type Regs386
```golang
  type Regs386 struct {
        AX    uint32
        BX    uint32
        CX    uint32
        DX    uint32
        DI    uint32
        SI    uint32
        BP    uint32
        SP    uint32
        SS    uint32
        FLAGS uint32
        IP    uint32
        CS    uint32
        DS    uint32
        ES    uint32
        FS    uint32
        GS    uint32
  }
  Regs386 is the Mach-O 386 register structure.
  386注册结构Mach-O
```
  
type RegsAMD64
```golang
  type RegsAMD64 struct {
        AX    uint64
        BX    uint64
        CX    uint64
        DX    uint64
        DI    uint64
        SI    uint64
        BP    uint64
        SP    uint64
        R8    uint64
        R9    uint64
        R10   uint64
        R11   uint64
        R12   uint64
        R13   uint64
        R14   uint64
        R15   uint64
        IP    uint64
        FLAGS uint64
        CS    uint64
        FS    uint64
        GS    uint64
  }
  RegsAMD64 is the Mach-O AMD64 register structure.
  AMD64注册结构Mach-O
```

type Section
```golang
  type Section struct {
        SectionHeader

        // Embed ReaderAt for ReadAt method.
        // Do not embed SectionReader directly
        // to avoid having Read and Seek.
        // If a client wants Read and Seek it must use
        // Open() to avoid fighting over the seek offset
        // with other clients.
        io.ReaderAt
        // contains filtered or unexported fields
  }
```

func (s *Section) Data() ([]byte, error)
```golang  
    func (s *Section) Data() ([]byte, error)
      Data reads and returns the contents of the Mach-O section.
      读取和返回Mach-O 章节内容
```

func (*Section) Open
```golang      
  func (s *Section) Open() io.ReadSeeker
    Open returns a new ReadSeeker reading the Mach-O section.
    返回一个新的ReadSeeker读取Mach-O 章节内容
```
    
type Section32
```golang
  type Section32 struct {
        Name     [16]byte
        Seg      [16]byte
        Addr     uint32
        Size     uint32
        Offset   uint32
        Align    uint32
        Reloff   uint32
        Nreloc   uint32
        Flags    uint32
        Reserve1 uint32
        Reserve2 uint32
  }
  A Section32 is a 32-bit Mach-O section header.
  32位 Mach-O 章节头
```

type Section64
```golang
  type Section64 struct {
        Name     [16]byte
        Seg      [16]byte
        Addr     uint64
        Size     uint64
        Offset   uint32
        Align    uint32
        Reloff   uint32
        Nreloc   uint32
        Flags    uint32
        Reserve1 uint32
        Reserve2 uint32
        Reserve3 uint32
  }
  A Section32 is a 64-bit Mach-O section header.
  64位 Mach-O 章节头
```
  
type SectionHeader
```golang
  type SectionHeader struct {
        Name   string
        Seg    string
        Addr   uint64
        Size   uint64
        Offset uint32
        Align  uint32
        Reloff uint32
        Nreloc uint32
        Flags  uint32
  }
```

type Segment
```golang
  type Segment struct {
        LoadBytes
        SegmentHeader

        // Embed ReaderAt for ReadAt method.
        // Do not embed SectionReader directly
        // to avoid having Read and Seek.
        // If a client wants Read and Seek it must use
        // Open() to avoid fighting over the seek offset
        // with other clients.
        io.ReaderAt
        // contains filtered or unexported fields
  }
  A Segment represents a Mach-O 32-bit or 64-bit load segment command.
  代表一个Mach-O 32位或64位的加载段命令
```

func (*Segment) Data
```golang  
    func (s *Segment) Data() ([]byte, error)
      Data reads and returns the contents of the segment.
      读取和返回段内容
```

func (*Segment) Open
```golang      
    func (s *Segment) Open() io.ReadSeeker
      Open returns a new ReadSeeker reading the segment.
        开个一个新的 ReadSeeker 读取 段。
```

type Segment32
```golang
  type Segment32 struct {
        Cmd     LoadCmd
        Len     uint32
        Name    [16]byte
        Addr    uint32
        Memsz   uint32
        Offset  uint32
        Filesz  uint32
        Maxprot uint32
        Prot    uint32
        Nsect   uint32
        Flag    uint32
  }
  A Segment32 is a 32-bit Mach-O segment load command.
  一个32位的Mach-O 段加载命令
```
  
type Segment64
```golang
  type Segment64 struct {
        Cmd     LoadCmd
        Len     uint32
        Name    [16]byte
        Addr    uint64
        Memsz   uint64
        Offset  uint64
        Filesz  uint64
        Maxprot uint32
        Prot    uint32
        Nsect   uint32
        Flag    uint32
  }
  A Segment64 is a 64-bit Mach-O segment load command.
   一个64位的Mach-O 段加载命令
```
  
type SegmentHeader
```golang
  type SegmentHeader struct {
        Cmd     LoadCmd
        Len     uint32
        Name    string
        Addr    uint64
        Memsz   uint64
        Offset  uint64
        Filesz  uint64
        Maxprot uint32
        Prot    uint32
        Nsect   uint32
        Flag    uint32
  }
  A SegmentHeader is the header for a Mach-O 32-bit or 64-bit load segment command.
  Mach-O 32位或64位 加载段命令的头
```
  
type Symbol
```golang
  type Symbol struct {
        Name  string
        Type  uint8
        Sect  uint8
        Desc  uint16
        Value uint64
  }
  A Symbol is a Mach-O 32-bit or 64-bit symbol table entry.
  是 一个 Mach-O 32位或64位 符号表项。
```
  
type Symtab
```golang
  type Symtab struct {
        LoadBytes
        SymtabCmd
        Syms []Symbol
  }
  A Symtab represents a Mach-O symbol table command.
  表示一个Mach-O  符号表命令
```

type SymtabCmd
```golang
  type SymtabCmd struct {
        Cmd     LoadCmd
        Len     uint32
        Symoff  uint32
        Nsyms   uint32
        Stroff  uint32
        Strsize uint32
  }
  A SymtabCmd is a Mach-O symbol table command.
  是一个Mach-O  符号表命令
```
  
type Thread
```golang
  type Thread struct {
        Cmd  LoadCmd
        Len  uint32
        Type uint32
        Data []uint32
  }
  A Thread is a Mach-O thread state command.
  是一个 Mach-O 线程状态命令
```

type Type
```golang
  type Type uint32
    A Type is a Mach-O file type, either an object or an executable.
    是一个Mach-O 文件 或者 对象或者 可执行文件  类型。
  
  const (
          TypeObj  Type = 1
          TypeExec Type = 2
  )
```