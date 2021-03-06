<pre>
package skiplist
    import "github.com/glenn-brown/skiplist"

    Package skiplist implements fast indexable ordered multimaps.

    This skip list has some features that make it special: It supports
    position-index addressing. It can act as a map or as a multimap. It
    automatically adjusts its depth. It mimics Go's container/list interface
    where possible. It automatically and efficiently supports int*, float*,
    uint*, string, and []byte keys. It supports externally defined key types
    via the FastKey and SlowKey interfaces.

    Get, Set, Insert, Remove*, Element*, and Pos operations all require
    O(log(N)) time or less, where N is the number of entries in the list.
    GetAll() requires O(log(N)+V) time where V is the number of values
    returned. The skiplist requires O(N) space.

TYPES

type Element struct {
    Value interface{}
    // contains filtered or unexported fields
}
    Element is an key/value pair inserted into the list. Use element.Key()
    to access the protected key.

func (e *Element) Key() interface{}
    Key returns the key used to insert the value in the list element in O(1)
    time.

func (e *Element) Next() *Element
    Next returns the next-higher-indexed list element or nil in O(1) time.

func (e *Element) String() string
    String returns a Key:Value string representation of the element.

type FastKey interface {
    Less(interface{}) bool
    Score() float64
}
    The FastKey interface allows externally-defined types to be used as
    keys, efficiently. An a.Less(b) call should return true iff a < b.
    key.Score() must increase monotonically as key increases.

type SlowKey interface {
    Less(interface{}) bool
}
    The SlowKey interface allows externally-defined types to be used as
    keys. An a.Less(b) call should return true iff a < b.

type T struct {
    // contains filtered or unexported fields
}
    A skiplist.T is a skiplist. A skiplist is linked at multiple levels. The
    bottom level (L0) is a sorted linked list of entries, and each link has
    a link at the next higher level added with probability P at insertion.
    Since this is a position-addressable skip-list, each link has an
    associated 'width' specifying the number of nodes it skips, so nodes can
    also be referenced by position.

    For example, a skiplist containing values from 0 to 0x16 might be
    structured like this:

	L4 |---------------------------------------------------------------------->/
	L3 |------------------------------------------->|------------------------->/
	L2 |---------->|---------->|---------->|------->|---------------->|---->|->/
	L1 |---------->|---------->|---------->|->|---->|->|->|->|------->|->|->|->/
	L0 |->|->|->|->|->|->|->|->|->|->|->|->|->|->|->|->|->|->|->|->|->|->|->|->/
	      0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  1  1  1  1  1  1  1
	      0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f  0  1  2  3  4  5  6

    The skiplist is searched starting at the top level, going as far right
    as possible without passing the desired Element, dropping down one
    level, and repeating for each level.

func New() *T
    New returns a new skiplist in O(1) time. The list will be sorted from
    least to greatest key.

func NewDescending() *T
    NewDescending is like New, except keys are sorted from greatest to
    least.

func (l *T) Element(key interface{}) (e *Element)
    Element returns the youngest list element for key, without modifying the
    list, in O(log(N)) time. If there is no match, nil is returned.

func (l *T) ElementN(index int) *Element
    ElementN returns the Element at position pos in the skiplist, in
    O(log(index)) time. If no such entry exists, nil is returned.

func (l *T) ElementPos(key interface{}) (e *Element, pos int)
    Element returns the youngest list element for key and its position, If
    there is no match, nil and -1 are returned.

    Consider using Get or GetAll instead if you only want Values.

func (l *T) Front() *Element
    Return the first list element in O(1) time.

func (l *T) Get(key interface{}) (value interface{})
    Get returns the value corresponding to key in the table in O(log(N))
    time. If there is no corresponding value, nil is returned. If there are
    multiple corresponding values, the youngest is returned.

    If the list might contain an nil value, you may want to use GetOk
    instead.

func (l *T) GetAll(key interface{}) (values []interface{})
    GetAll returns all values coresponding to key in the list, starting with
    the youngest. If no value corresponds, an empty slice is returned.
    O(log(N)+V) time is required, where M is the number of values returned.

func (l *T) GetOk(key interface{}) (value interface{}, ok bool)
    GetOk returns the value corresponding to key in the table in O(log(N))
    time. The return value ok is true iff the key was present. If there is
    no corresponding value, nil and false are returned. If there are
    multiple corresponding values, the youngest is returned.

func (l *T) Insert(key interface{}, value interface{}) *T
    Insert a {key,value} pair into the skip list in O(log(N)) time.

func (l *T) Len() int
    Len returns the number of elements in the T.

func (l *T) Pos(key interface{}) (pos int)
    ElementPos returns the position of the youngest list element for key,
    without modifying the list, in O(log(N)) time. If there is no match, -1
    is returned.

    Consider using Get or GetAll instead if you only want Values.

func (l *T) Remove(key interface{}) *Element
    Remove the youngest Element associate with Key, if any, in O(log(N))
    time. Return the removed element or nil.

func (l *T) RemoveElement(e *Element) *Element
    Remove the specified element from the table, in O(log(N)) time. If the
    element is one of M multiple entries for the key, and additional O(M)
    time is required. This is useful for removing a specific element in a
    multimap, or removing elements during iteration.

func (l *T) RemoveN(index int) *Element
    RemoveN removes any element at position pos in O(log(N)) time, returning
    it or nil.

func (l *T) Set(key interface{}, value interface{}) *T
    Insert a {key,value} pair into the skip list in O(log(N)) time,
    replacing the youngest entry for key, if any.

func (l *T) String() string
    Function String prints only the key/value pairs in the skip list.


</pre>
