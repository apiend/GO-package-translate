包地址：http://golang.org/pkg/container/ring/
Package ring implements operations on circular lists. 
ring实现循环链表的操作。


type Ring
```golang
  type Ring struct {
        Value interface{} // for use by client; untouched by this library
        // contains filtered or unexported fields
  }
  A Ring is an element of a circular list, or ring. 
  Rings do not have a beginning or end; a pointer to any ring element serves as reference to the entire ring. 
  Empty rings are represented as nil Ring pointers. 
  The zero value for a Ring is a one-element ring with a nil Value.
  Ring是一个循环链表的一个元素。
  Rings 没有开始或结束。任何ring的指针都以整个ring做为参照。
   空的ring代表nil指针。
  ring的零值是一个值为nil的ring元素。
```

func New
```golang
    func New(n int) *Ring
      New creates a ring of n elements. 
      	创建一个 n个元素的的ring
```

func (*Ring) Do
```golang
    func (r *Ring) Do(f func(interface{}))
      Do calls function f on each element of the ring, in forward order. 
      The behavior of Do is undefined if f changes *r. 
     	Do 根据正向顺序 给每个ring元素调用f
       如果f 改变了*r 那Do是 undefined
```

func (*Ring) Len
```golang    
    func (r *Ring) Len() int
      Len computes the number of elements in ring r. 
      It executes in time proportional to the number of elements. 
      Len 计算r的元素数量。它的执行时间是按元素数量的比例
```

func (*Ring) Link
```golang    
    func (r *Ring) Link(s *Ring) *Ring
       Link connects ring r with ring s such that r.Next() becomes s and returns the original value for r.Next(). r must not be empty.
       If r and s point to the same ring, linking them removes the elements between r and s from the ring. 
       The removed elements form a subring and the result is a reference to that subring (if no elements were removed, 
       the result is still the original value for r.Next(), and not nil).
       
       If r and s point to different rings, linking them creates a single ring with the elements of s inserted after r. 
       The result points to the element following the last element of s after insertion.
       
         把ring r和 rings 连接起来，就像 让 r.Next() 变成s，返回r.Next()的原始值。r必须不为空。
         如果r和s指向相同的ring，删除r和s之间的元素然后连接。
         删除的元素形成一个子环，结果 是一个子环。 （如果没有元素被删除，那结果依旧是r.Next() 原来的值，而不是nil）
         
         如果r和s指向不同的rings，s插入到r之后 组成一个ring
         结果指向s的最后一个元素
```

func (*Ring) Move    
```golang
    func (r *Ring) Move(n int) *Ring
    Move moves n % r.Len() elements backward (n < 0) or forward (n >= 0) in the ring and returns that ring element. r must not be empty.
   	 移动 n % r.Len() 元素  往后（n<0）或  往前(n>=0)，返回ring元素。r必须不为空
```    

func (*Ring) Next
```golang
	func (r *Ring) Next() *Ring
      Next returns the next ring element. r must not be empty. 
        返回下一个ring元素。r必须非空
```

func (*Ring) Prev
```golang
    func (r *Ring) Prev() *Ring
      Prev returns the previous ring element. r must not be empty. 
        返回ring之前的元素。
```

```golang
    func (r *Ring) Unlink(n int) *Ring
      Unlink removes n % r.Len() elements from the ring r, starting at r.Next(). If n % r.Len() == 0, r remains unchanged. 
      The result is the removed subring. r must not be empty. 
      Unlink删除n % r.Len()的元素数。从 r.Next()开始。如果 n % r.Len() == 0，r保持不变。结果是删除子环，r必须非空
```      


      
