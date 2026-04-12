---
title: "Containers and Memory Management"
source: "https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
## B.4 Containers and Memory Management
> ## B.4 容器和内存管理

A variety of container data structures that extend those made available by the standard library are provided in the file [util/containers.h](https://github.com/mmp/pbrt-v4/tree/master/src/pbrt/util/containers.h).
> 文件 [util/containers.h](https://github.com/mmp/pbrt-v4/tree/master/src/pbrt/util/containers.h) 中提供了多种扩展标准库容器的数据结构。

First, there is InlinedVector. We will not describe its implementation here, but note that it is an extended version of std::vector that has storage for a handful of vector elements preallocated in its class definition. Thus, for short vectors, it can be used without incurring the cost of dynamic memory allocation. It is used extensively in the [Image](https://pbr-book.org/4ed/Utilities/Images.html#Image) class, for example.

Its class declaration is of the form:

template <typename T, int N, class Allocator = /\*... \*/> class InlinedVector;

The value of N specifies the number of elements to handle via the inline allocation in the class definition.

Even though the C++ standard library provides a hash table via std::unordered\_map, pbrt additionally provides a HashMap, also not included here. There are two reasons it exists: first, the hash table in the standard library is specified such that pointers to elements in the hash table will not change even if the table is resized, which in turn requires dynamic memory allocation for each element. Second, the GPU rendering path requires a hash table that can be used from GPU code. Its class declaration is of the form:

template <typename Key, typename Value, typename Hash = std::hash<Key>, typename Allocator = /\*... \*/> class HashMap;

Its main methods have the following signatures:

void Insert(const Key &key, const Value &value); bool HasKey(const Key &key) const; const Value &operator\[\](const Key &key) const;

### B.4.1 2D Arrays

While it is not difficult to index into a 1D memory buffer that represents a 2D array of values, having a template class that handles this task helps make code elsewhere in the system less verbose and easier to verify. Array2D fills this role in pbrt.

<<Array2D Definition>>=

template <typename T> class Array2D { public: <<Array2D Type Definitions>>

using value\_type = T; using iterator = value\_type \*; using const\_iterator = const value\_type \*; using allocator\_type = pstd::pmr::polymorphic\_allocator<std::byte>;

<< [Array2D Public Methods](#fragment-Array2DPublicMethods-0) >>

Array2D(allocator\_type [allocator](#Array2D::allocator) = {}): Array2D({{0, 0}, {0, 0}}, [allocator](#Array2D::allocator)) {} Array2D([Bounds2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2i) [extent](#Array2D::extent), Allocator [allocator](#Array2D::allocator) = {}): [extent](#Array2D::extent) ([extent](#Array2D::extent)), [allocator](#Array2D::allocator) ([allocator](#Array2D::allocator)) { int n = [extent](#Array2D::extent).[Area](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::Area) (); [values](#Array2D::values) = [allocator](#Array2D::allocator).[allocate\_object](https://pbr-book.org/4ed/Introduction/Using_and_Understanding_the_Code.html#std::pmr::polymorphic_allocator::allocate_object) <T>(n); for (int i = 0; i < n; ++i) [allocator](#Array2D::allocator).construct([values](#Array2D::values) + i); } Array2D([Bounds2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2i) [extent](#Array2D::extent), T def, allocator\_type [allocator](#Array2D::allocator) = {}): Array2D([extent](#Array2D::extent), [allocator](#Array2D::allocator)) { std::fill(begin(), end(), def); } template <typename InputIt, typename = typename std::enable\_if\_t<!std::is\_integral<InputIt>::value && std::is\_base\_of< std::input\_iterator\_tag, typename std::iterator\_traits<InputIt>::iterator\_category>::value>> Array2D(InputIt first, InputIt last, int nx, int ny, allocator\_type [allocator](#Array2D::allocator) = {}): Array2D({{0, 0}, {nx, ny}}, [allocator](#Array2D::allocator)) { std::copy(first, last, begin()); } Array2D(int nx, int ny, allocator\_type [allocator](#Array2D::allocator) = {}): Array2D({{0, 0}, {nx, ny}}, [allocator](#Array2D::allocator)) {} Array2D(int nx, int ny, T def, allocator\_type [allocator](#Array2D::allocator) = {}): Array2D({{0, 0}, {nx, ny}}, def, [allocator](#Array2D::allocator)) {} Array2D(const Array2D &a, allocator\_type [allocator](#Array2D::allocator) = {}): Array2D(a.begin(), a.end(), a.XSize(), a.YSize(), [allocator](#Array2D::allocator)) {} ~Array2D() { int n = [extent](#Array2D::extent).[Area](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::Area) (); for (int i = 0; i < n; ++i) [allocator](#Array2D::allocator).destroy([values](#Array2D::values) + i); [allocator](#Array2D::allocator).deallocate\_object([values](#Array2D::values), n); } Array2D(Array2D &&a, allocator\_type [allocator](#Array2D::allocator) = {}): [extent](#Array2D::extent) (a.[extent](#Array2D::extent)), [allocator](#Array2D::allocator) ([allocator](#Array2D::allocator)) { if ([allocator](#Array2D::allocator) == a.[allocator](#Array2D::allocator)) { [values](#Array2D::values) = a.[values](#Array2D::values); a.[extent](#Array2D::extent) = [Bounds2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2i) ({0, 0}, {0, 0}); a.[values](#Array2D::values) = nullptr; } else { [values](#Array2D::values) = [allocator](#Array2D::allocator).[allocate\_object](https://pbr-book.org/4ed/Introduction/Using_and_Understanding_the_Code.html#std::pmr::polymorphic_allocator::allocate_object) <T>([extent](#Array2D::extent).[Area](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::Area) ()); std::copy(a.begin(), a.end(), begin()); } } Array2D &operator=(const Array2D &a) = delete; Array2D &operator=(Array2D &&other) { if ([allocator](#Array2D::allocator) == other.[allocator](#Array2D::allocator)) { pstd::swap([extent](#Array2D::extent), other.[extent](#Array2D::extent)); pstd::swap([values](#Array2D::values), other.[values](#Array2D::values)); } else if ([extent](#Array2D::extent) == other.[extent](#Array2D::extent)) { int n = [extent](#Array2D::extent).[Area](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::Area) (); for (int i = 0; i < n; ++i) { [allocator](#Array2D::allocator).destroy([values](#Array2D::values) + i); [allocator](#Array2D::allocator).construct([values](#Array2D::values) + i, other.[values](#Array2D::values) \[i\]); } [extent](#Array2D::extent) = other.[extent](#Array2D::extent); } else { int n = [extent](#Array2D::extent).[Area](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::Area) (); for (int i = 0; i < n; ++i) [allocator](#Array2D::allocator).destroy([values](#Array2D::values) + i); [allocator](#Array2D::allocator).deallocate\_object([values](#Array2D::values), n); int no = other.[extent](#Array2D::extent).[Area](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::Area) (); [values](#Array2D::values) = [allocator](#Array2D::allocator).[allocate\_object](https://pbr-book.org/4ed/Introduction/Using_and_Understanding_the_Code.html#std::pmr::polymorphic_allocator::allocate_object) <T>(no); for (int i = 0; i < no; ++i) [allocator](#Array2D::allocator).construct([values](#Array2D::values) + i, other.[values](#Array2D::values) \[i\]); } return \*this; } T &operator\[\]([Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) p) { [DCHECK](https://pbr-book.org/4ed/Utilities/User_Interaction.html#DCHECK) ([InsideExclusive](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::InsideExclusive) (p, [extent](#Array2D::extent))); p.x -= [extent](#Array2D::extent).[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).x; p.y -= [extent](#Array2D::extent).[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).y; return [values](#Array2D::values) \[p.x + ([extent](#Array2D::extent).[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).x - [extent](#Array2D::extent).[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).x) \* p.y\]; } PBRT\_CPU\_GPU const T &operator()(int x, int y) const { return (\*this)\[{x, y}\]; } PBRT\_CPU\_GPU const T &operator\[\]([Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) p) const { [DCHECK](https://pbr-book.org/4ed/Utilities/User_Interaction.html#DCHECK) ([InsideExclusive](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::InsideExclusive) (p, [extent](#Array2D::extent))); p.x -= [extent](#Array2D::extent).[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).x; p.y -= [extent](#Array2D::extent).[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).y; return [values](#Array2D::values) \[p.x + ([extent](#Array2D::extent).[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).x - [extent](#Array2D::extent).[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).x) \* p.y\]; } int size() const { return [extent](#Array2D::extent).[Area](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::Area) (); } int XSize() const { return [extent](#Array2D::extent).[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).x - [extent](#Array2D::extent).[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).x; } int YSize() const { return [extent](#Array2D::extent).[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).y - [extent](#Array2D::extent).[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).y; } iterator begin() { return [values](#Array2D::values); } iterator end() { return begin() + size(); } PBRT\_CPU\_GPU const\_iterator begin() const { return [values](#Array2D::values); } PBRT\_CPU\_GPU const\_iterator end() const { return begin() + size(); } PBRT\_CPU\_GPU operator pstd::span<T>() { return pstd::span<T>([values](#Array2D::values), size()); } PBRT\_CPU\_GPU operator pstd::span<const T>() const { return pstd::span<const T>([values](#Array2D::values), size()); } std::string ToString() const { std::string s = StringPrintf("\[ Array2D [extent](#Array2D::extent): %s [values](#Array2D::values): \[", [extent](#Array2D::extent)); for (int y = [extent](#Array2D::extent).[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).y; y < [extent](#Array2D::extent).[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).y; ++y) { s += " \[ "; for (int x = [extent](#Array2D::extent).[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).x; x < [extent](#Array2D::extent).[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).x; ++x) { T value = (\*this)(x, y); s += StringPrintf("%s, ", value); } s += "\], "; } s += " \] \]"; return s; }

private: << [Array2D Private Members](#fragment-Array2DPrivateMembers-0) >>

[Bounds2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2i) extent; Allocator allocator; T \*values;

};

The array is defined over a 2D region specified by extent; its lower bounds do not necessarily need to be at.

<<Array2D Private Members>>=

[Bounds2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2i) extent; Allocator allocator; T \*values;

Array2D provides a variety of constructors, including ones that initialize its entries with a constant value or via a start and ending iterator. Here is the one that default-initializes the entries.

<<Array2D Public Methods>>=

Array2D([Bounds2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2i) [extent](#Array2D::extent), Allocator [allocator](#Array2D::allocator) = {}): [extent](#Array2D::extent) ([extent](#Array2D::extent)), [allocator](#Array2D::allocator) ([allocator](#Array2D::allocator)) { int n = [extent](#Array2D::extent).[Area](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::Area) (); [values](#Array2D::values) = [allocator](#Array2D::allocator).[allocate\_object](https://pbr-book.org/4ed/Introduction/Using_and_Understanding_the_Code.html#std::pmr::polymorphic_allocator::allocate_object) <T>(n); for (int i = 0; i < n; ++i) [allocator](#Array2D::allocator).construct([values](#Array2D::values) + i); }

The array can be indexed using a [Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i), which should be inside the specified extent. After translating the point by the origin of the bounds, the usual indexing computation is performed to find the value. Array2D also provides a const version of this method as well as an operator() that takes a pair of integers.

A few methods give the total size and sizes of individual dimensions of the array.

int size() const { return [extent](#Array2D::extent).[Area](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::Area) (); } int XSize() const { return [extent](#Array2D::extent).[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).x - [extent](#Array2D::extent).pMin.x; } int YSize() const { return [extent](#Array2D::extent).[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).y - [extent](#Array2D::extent).pMin.y; }

It is also possible to iterate over elements of the array directly.

<<Array2D Public Methods>>+=

iterator begin() { return values; } iterator end() { return begin() + size(); }

### B.4.2 Interned Objects

If many instances of the same object are stored in memory, especially if the objects are large, the *interning* technique can be helpful. With it, a single copy of each unique object is stored and all uses of it refer to that copy. (The technique is thus generally only useful for read-only data.) pbrt uses interning both for transformations found in the scene description and for strings in the scene entity objects defined in Section [C.2.1](https://pbr-book.org/4ed/Processing_the_Scene_Description/Managing_the_Scene_Description.html#sec:scene-entities). For complex scenes, the memory savings from eliminating redundant copies can be large.

The InternCache class manages such caches. It is a template class based on the type being managed and its hash function. Types managed by it must provide an equality operator so that it can find matches.

template <typename T, typename Hash = std::hash<T>> class InternCache;

Beyond the constructor, [InternCache](#InternCache) provides two variations of a single method, Lookup(). Their signatures are below. Both store a single copy of provided objects in a hash table, using a mutex to allow concurrent access by multiple threads. The first Lookup() method allocates memory for the object itself using the allocator passed to the [InternCache](#InternCache) constructor and copies the provided item to initialize the object stored in the cache. The second takes a user-provided creation callback function with the signature shown below. This allows for more complex object initialization—as is used in the [LightBase::LookupSpectrum()](https://pbr-book.org/4ed/Light_Sources/Light_Interface.html#LightBase::LookupSpectrum) method, for example.

const T \*Lookup(const T &item); /\* F: T \*create(Allocator alloc, const T &item) \*/ template <typename F> const T \*Lookup(const T &item, F create);

Note that the Lookup() methods return a pointer to the shared instance of the object. They always return the same pointer for equal objects, so a pointer equality test can be used to test for equality with values returned by the cache. For large or complex objects, more efficient equality tests can be a further benefit of interning.

InternedString is a convenience class for strings stored in an [InternCache](#InternCache). Using it makes it clear that a string pointer refers to an interned string, which helps clarify code.

<<InternedString Definition>>=

class InternedString { public: << [InternedString Public Methods](#fragment-InternedStringPublicMethods-0) >>

InternedString(const std::string \*str): str(str) {} operator const std::string &() const { return \*str; } bool operator==(const char \*s) const { return \*str == s; } bool operator==(const std::string &s) const { return \*str == s; } bool operator!=(const char \*s) const { return \*str!= s; } bool operator!=(const std::string &s) const { return \*str!= s; } bool operator<(const char \*s) const { return \*str < s; } bool operator<(const std::string &s) const { return \*str < s; } std::string ToString() const { return \*str; }

private: const std::string \*str = nullptr; };

It also provides an automatic conversion operator to std::string, saving users from needing to dereference the pointer themselves. Comparison operators with strings and const char \* s are also available.

<<InternedString Public Methods>>=

InternedString(const std::string \*str): str(str) {} operator const std::string &() const { return \*str; }

### B.4.3 Collections of Types

In pbrt ’s wavefront rendering path, it was useful to perform various operations on collections of types (e.g., to instantiate a template function for each of the possible [Material](https://pbr-book.org/4ed/Textures_and_Materials/Material_Interface_and_Implementations.html#Material) types). There is no direct support for such operations in C++, but with some application of template programming it is possible to provide these capabilities.

First, we define TypePack, a structure that holds no non- static data. Its purpose is to define a type that represents a collection of types—those provided in the template parameter pack. It also provides a handy count member variable that gives the number of types.

<<TypePack Definition>>=

template <typename... Ts> struct TypePack { static constexpr size\_t count = sizeof...(Ts); };

IndexOf provides the index of a given type among the types in a TypePack. Here is the declaration of the structure for its general template, which will only be instantiated if the given type is not in fact one of the types in a type pack. We can use a C++ trick to ensure a reasonable error message is printed in this case: because the following static\_assert ’s condition can only be evaluated at compile time given a concrete type T (even though it will clearly always be false), the error message is thus only printed if this version of IndexOf is instantiated.

<<TypePack Operations>>=

template <typename T, typename... Ts> struct IndexOf { static constexpr int count = 0; static\_assert(!std::is\_same\_v<T, T>, "Type not present in TypePack"); };

A first template specialization handles the case where the first type in the TypePack matches the given type T. In this case, the index is zero.

<<TypePack Operations>>+=

template <typename T, typename... Ts> struct IndexOf<T, TypePack<T, Ts...>> { static constexpr int count = 0; };

Another template specialization handles the case where T is not the first type. One is added to the final count, and a recursive template instantiation checks the next type. Note that because all the types involved are known at compile time, the final value is a compile-time constant (as evidenced by the constexpr qualifier).

<<TypePack Operations>>+=

template <typename T, typename U, typename... Ts> struct IndexOf<T, TypePack<U, Ts...>> { static constexpr int count = 1 + IndexOf<T, TypePack<Ts...>>::count; };

We will find it useful to be able to wrap a template class around each of a set of types. This operation is provided by MapType. The base case is a single-element type pack.

<<TypePack Operations>>+=

template <template <typename> class M, typename T> struct MapType<M, TypePack<T>> { using type = TypePack<M<T>>; };

Larger numbers of types are handled recursively. Prepend, not included here, gives the TypePack that results from prepending a given type to a TypePack of others.

<<TypePack Operations>>+=

template <template <typename> class M, typename T, typename... Ts> struct MapType<M, TypePack<T, Ts...>> { using type = typename Prepend<M<T>, typename MapType<M, TypePack<Ts...>>::type>::type; };

Finally, we will define a ForEachType() function, which calls the provided function (which is assumed to be a template function) once for each of the types in a TypePack. The general case peels off the first type, calls the provided function, and then proceeds with a recursive call with the remainder of types in the TypePack. In this case, the recursion is expressed in a slightly different manner, via a temporary instance of a TypePack -typed variable that is used purely to record the types yet to be handled.

<<TypePack Operations>>+=

template <typename F, typename T, typename... Ts> void ForEachType(F func, TypePack<T, Ts...>) { func.template operator()<T>(); ForEachType(func, TypePack<Ts...>()); }

The base case of an empty TypePack ends the recursion.

<<TypePack Operations>>+=

template <typename F> void ForEachType(F func, TypePack<>) {}

### B.4.4 Tagged Pointers

The TaggedPointer class is at the heart of how pbrt handles polymorphic types. It takes the pointer to an object of known type and uses excess bits in its pointer to encode the object’s actual type (i.e., to *tag* it). When dynamic dispatch or other type-specific operations are needed, the object’s type can be extracted from the pointer. This class’s implementation is in the file [util/taggedptr.h](https://github.com/mmp/pbrt-v4/tree/master/src/pbrt/util/taggedptr.h).

TaggedPointer is a template class that requires all the types it may represent to be provided at compile time. Note that this approach thus precludes runtime loading of additional class definitions of new types, as would be possible with the usual approach to polymorphism based on virtual functions.

<<TaggedPointer Definition>>=

template <typename... Ts> class TaggedPointer { public: << [TaggedPointer Public Types](#fragment-TaggedPointerPublicTypes-0) >>

using Types = TypePack<Ts...>;

<< [TaggedPointer Public Methods](#fragment-TaggedPointerPublicMethods-0) >>

template <typename T> [TaggedPointer](#TaggedPointer) (T \* [ptr](#TaggedPointer::ptr)) { uintptr\_t iptr = reinterpret\_cast<uintptr\_t>([ptr](#TaggedPointer::ptr)); constexpr unsigned int type = [TypeIndex](#TaggedPointer::TypeIndex) <T>(); [bits](#TaggedPointer::bits) = iptr | ((uintptr\_t)type << [tagShift](#TaggedPointer::tagShift)); } PBRT\_CPU\_GPU [TaggedPointer](#TaggedPointer) (std::nullptr\_t np) {} PBRT\_CPU\_GPU [TaggedPointer](#TaggedPointer) (const [TaggedPointer](#TaggedPointer) &t) { [bits](#TaggedPointer::bits) = t.[bits](#TaggedPointer::bits); } PBRT\_CPU\_GPU [TaggedPointer](#TaggedPointer) &operator=(const [TaggedPointer](#TaggedPointer) &t) { [bits](#TaggedPointer::bits) = t.[bits](#TaggedPointer::bits); return \*this; } template <typename T> static constexpr unsigned int [TypeIndex](#TaggedPointer::TypeIndex) () { using Tp = typename std::remove\_cv\_t<T>; if constexpr (std::is\_same\_v<Tp, std::nullptr\_t>) return 0; else return 1 + pbrt::[IndexOf](#IndexOf) <Tp, Types>::count; } unsigned int [Tag](#TaggedPointer::Tag) () const { return (([bits](#TaggedPointer::bits) & tagMask) >> [tagShift](#TaggedPointer::tagShift)); } template <typename T> bool [Is](#TaggedPointer::Is) () const { return [Tag](#TaggedPointer::Tag) () == [TypeIndex](#TaggedPointer::TypeIndex) <T>(); } static constexpr unsigned int MaxTag() { return sizeof...(Ts); } PBRT\_CPU\_GPU explicit operator bool() const { return ([bits](#TaggedPointer::bits) & [ptrMask](#TaggedPointer::ptrMask))!= 0; } PBRT\_CPU\_GPU bool operator<(const [TaggedPointer](#TaggedPointer) &tp) const { return [bits](#TaggedPointer::bits) < tp.[bits](#TaggedPointer::bits); } template <typename T> T \*Cast() { return reinterpret\_cast<T \*>([ptr](#TaggedPointer::ptr) ()); } template <typename T> const T \*Cast() const { return reinterpret\_cast<const T \*>([ptr](#TaggedPointer::ptr) ()); } template <typename T> T \*CastOrNullptr() { if ([Is](#TaggedPointer::Is) <T>()) return reinterpret\_cast<T \*>([ptr](#TaggedPointer::ptr) ()); else return nullptr; } template <typename T> PBRT\_CPU\_GPU const T \*CastOrNullptr() const { if ([Is](#TaggedPointer::Is) <T>()) return reinterpret\_cast<const T \*>([ptr](#TaggedPointer::ptr) ()); else return nullptr; } std::string ToString() const { return StringPrintf("\[ [TaggedPointer](#TaggedPointer) [ptr](#TaggedPointer::ptr): 0x%p tag: %d \]", [ptr](#TaggedPointer::ptr) (), [Tag](#TaggedPointer::Tag) ()); } PBRT\_CPU\_GPU bool operator==(const [TaggedPointer](#TaggedPointer) &tp) const { return [bits](#TaggedPointer::bits) == tp.[bits](#TaggedPointer::bits); } PBRT\_CPU\_GPU bool operator!=(const [TaggedPointer](#TaggedPointer) &tp) const { return [bits](#TaggedPointer::bits)!= tp.[bits](#TaggedPointer::bits); } void \* [ptr](#TaggedPointer::ptr) () { return reinterpret\_cast<void \*>([bits](#TaggedPointer::bits) & [ptrMask](#TaggedPointer::ptrMask)); } const void \* [ptr](#TaggedPointer::ptr) () const { return reinterpret\_cast<const void \*>([bits](#TaggedPointer::bits) & [ptrMask](#TaggedPointer::ptrMask)); } template <typename F> PBRT\_CPU\_GPU decltype(auto) Dispatch(F &&func) { using R = typename detail::ReturnType<F, Ts...>::type; return detail::Dispatch<F, R, Ts...>(func, [ptr](#TaggedPointer::ptr) (), [Tag](#TaggedPointer::Tag) () - 1); } template <typename F> PBRT\_CPU\_GPU decltype(auto) Dispatch(F &&func) const { DCHECK([ptr](#TaggedPointer::ptr) ()); using R = typename detail::ReturnType<F, Ts...>::type; return detail::Dispatch<F, R, Ts...>(func, [ptr](#TaggedPointer::ptr) (), [Tag](#TaggedPointer::Tag) () - 1); } template <typename F> decltype(auto) DispatchCPU(F &&func) { DCHECK([ptr](#TaggedPointer::ptr) ()); using R = typename detail::ReturnType<F, Ts...>::type; return detail::DispatchCPU<F, R, Ts...>(func, [ptr](#TaggedPointer::ptr) (), [Tag](#TaggedPointer::Tag) () - 1); } template <typename F> decltype(auto) DispatchCPU(F &&func) const { DCHECK([ptr](#TaggedPointer::ptr) ()); using R = typename detail::ReturnTypeConst<F, Ts...>::type; return detail::DispatchCPU<F, R, Ts...>(func, [ptr](#TaggedPointer::ptr) (), [Tag](#TaggedPointer::Tag) () - 1); }

private: << [TaggedPointer Private Members](#fragment-TaggedPointerPrivateMembers-0) >>

static constexpr int [tagShift](#TaggedPointer::tagShift) = 57; static constexpr int [tagBits](#TaggedPointer::tagBits) = 64 - [tagShift](#TaggedPointer::tagShift); static constexpr uint64\_t [tagMask](#TaggedPointer::tagMask) = ((1ull << [tagBits](#TaggedPointer::tagBits)) - 1) << [tagShift](#TaggedPointer::tagShift); static constexpr uint64\_t ptrMask = ~ [tagMask](#TaggedPointer::tagMask); uintptr\_t bits = 0;

};

All the possible types for a tagged pointer are provided via a public type definition.

<<TaggedPointer Public Types>>=

using Types = TypePack<Ts...>;

Modern processors ubiquitously use 64-bit pointers, which allow addressing bytes of memory. Memory sizes of tens to hundreds of gigabytes are common now, which is a far cry from the *billions* of gigabytes that a 64-bit pointer can address. Therefore, processors specify the size of their addressable memory space in terms of a smaller number of bits. Until recently, a 48-bit address space was common on CPUs, though that has recently increased to 57 bits. While it is still unimaginable for a single system to have bytes of RAM, large address spaces can be useful for cluster computing where many machines present a unified address space or for mapping pointers to data in offline storage.

TaggedPointer therefore steals the upper bits of pointers in order to encode types. Even with 57-bit address spaces, there are still 7 bits left, which allows types, far more than pbrt needs.

<<TaggedPointer Private Members>>=

static constexpr int [tagShift](#TaggedPointer::tagShift) = 57; static constexpr int tagBits = 64 - [tagShift](#TaggedPointer::tagShift);

tagMask is a bitmask that extracts the type tag’s bits, and ptrMask extracts the original pointer.

static constexpr uint64\_t [tagMask](#TaggedPointer::tagMask) = ((1ull << [tagBits](#TaggedPointer::tagBits)) - 1) << [tagShift](#TaggedPointer::tagShift); static constexpr uint64\_t ptrMask = ~ [tagMask](#TaggedPointer::tagMask);

We can now implement the primary TaggedPointer constructor. Given a pointer of known type T, it uses the TypeIndex() method to get an integer index for its type. In turn, the bits member is set by combining the original pointer with the integer type, shifted up into the unused bits of the pointer value.

<<TaggedPointer Public Methods>>=

template <typename T> [TaggedPointer](#TaggedPointer) (T \*ptr) { uintptr\_t iptr = reinterpret\_cast<uintptr\_t>(ptr); constexpr unsigned int type = [TypeIndex](#TaggedPointer::TypeIndex) <T>(); [bits](#TaggedPointer::bits) = iptr | ((uintptr\_t)type << [tagShift](#TaggedPointer::tagShift)); }

<<TaggedPointer Private Members>>+=

uintptr\_t bits = 0;

Most of the work for the TypeIndex() method is done by the [IndexOf](#IndexOf) structure defined in the previous section. One more index is needed to represent a null pointer, however, so an index of 0 is used for it and the rest have one added to them.

template <typename T> static constexpr unsigned int TypeIndex() { using Tp = typename std::remove\_cv\_t<T>; if constexpr (std::is\_same\_v<Tp, std::nullptr\_t>) return 0; else return 1 + pbrt::[IndexOf](#IndexOf) <Tp, Types>::count; }

Tag() returns a TaggedPointer ’s tag by extracting the relevant bits. In turn, the Is() method performs a runtime check of whether a TaggedPointer represents a particular type.

unsigned int [Tag](#TaggedPointer::Tag) () const { return ((bits & tagMask) >> tagShift); } template <typename T> bool Is() const { return [Tag](#TaggedPointer::Tag) () == [TypeIndex](#TaggedPointer::TypeIndex) <T>(); }

The maximum value of a tag is equal to the number of represented types.

static constexpr unsigned int MaxTag() { return sizeof...(Ts); }

A pointer of a specified type is returned by CastOrNullptr(). As the name suggests, it returns nullptr if the TaggedPointer does not in fact hold an object of type T. In addition to this method, TaggedPointer also provides a const variant that returns a const T \* as well as unsafe Cast() methods that always return a pointer of the given type. Those should only be used when there is no question about the underlying type held by a TaggedPointer.

template <typename T> T \*CastOrNullptr() { if ([Is](#TaggedPointer::Is) <T>()) return reinterpret\_cast<T \*>([ptr](#TaggedPointer::ptr) ()); else return nullptr; }

For cases where the original pointer is needed but void pointer will suffice, the ptr() method is available. It has a const variant as well.

void \*ptr() { return reinterpret\_cast<void \*>([bits](#TaggedPointer::bits) & [ptrMask](#TaggedPointer::ptrMask)); }

The most interesting TaggedPointer method is Dispatch(), which is at the heart of pbrt ’s dynamic dispatch mechanism for polymorphic types. Its task is to determine which type of object a TaggedPointer points to and then call the provided function, passing it the object’s pointer, cast to the correct type. (See the [Spectrum::operator()](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Representing_Spectral_Distributions.html#Spectrum::operator) method, which calls TaggedPointer::Dispatch(); details about the operation of the function that is provided to Dispatch() are discussed with its implementation.)

Most of the work is done by standalone Dispatch() functions that are defined in a detail namespace, signifying that although they are defined in a header file, they should not be used by code outside of the header. Those functions require the return type of the provided function, which is determined by the ReturnType helper template. We will not include ReturnType ’s implementation here; it uses C++ template pack expansion to find the return type of func when called with each of the types that the TaggedPointer can hold, issues a compile time error if they are not all the same, and provides the return type via its definition of type.

<<TaggedPointer Public Methods>>+=

template <typename F> PBRT\_CPU\_GPU decltype(auto) Dispatch(F &&func) { using R = typename detail::ReturnType<F, Ts...>::type; return detail::Dispatch<F, R, Ts...>(func, [ptr](#TaggedPointer::ptr) (), [Tag](#TaggedPointer::Tag) () - 1); }

detail::Dispatch() may be called with an arbitrary number of types to handle, depending on how many a TaggedPointer manages. This is handled by providing a number of template specializations for different numbers of such types.

Early in the development of this version of pbrt, we implemented a dispatch mechanism that applied binary search, making a series of recursive function calls based on the type index until the corresponding type was found. That had equivalent performance to the approach implemented here and entailed fewer lines of code. However, we found that it cluttered call stacks, which was a nuisance when debugging. With the current approach, dynamic dispatch only imposes a single function call.

As an example of a Dispatch() function, here is the implementation of the one that handles three types; it is parameterized by the type of the callback function F and its return type R as well. All that there is to it is a switch statement to call the function with the appropriate pointer type based on the index passed in from [TaggedPointer::Dispatch()](#TaggedPointer::Dispatch).

<<TaggedPointer Helper Templates>>=

template <typename F, typename R, typename T0, typename T1, typename T2> R Dispatch(F &&func, void \*ptr, int index) { switch (index) { case 0: return func((T0 \*)ptr); case 1: return func((T1 \*)ptr); default: return func((T2 \*)ptr); } }

There are implementations of detail::Dispatch() for up to 8 types. If more are provided, a fallback implementation handles the first 8 and then makes a recursive call to detail::Dispatch() with the rest of them for larger indices. For pbrt ’s uses, where there are at most 10 or so types, this approach works well.

TaggedPointer also includes a const -qualified dispatch method as well as DispatchCPU(), which is necessary for methods that are only able to run on the CPU. (The default Dispatch() method requires that the method be callable from both CPU or GPU code, which is the most common use case in pbrt.) These both have corresponding dispatch functions in the detail namespace.

### B.4.5 3D Sampled Data

SampledGrid represents a point-sampled function over the domain. It is in a sense the 3D generalization of the [Image](https://pbr-book.org/4ed/Utilities/Images.html#Image) class, though it offers far fewer capabilities. Its main use in pbrt is as a representation for the [GridMedium](https://pbr-book.org/4ed/Volume_Scattering/Media.html#GridMedium) used to represent volumetric media. It is templated on a type T that represents the point-sampled values.

<<SampledGrid Definition>>=

template <typename T> class SampledGrid { public: << [SampledGrid Public Methods](#fragment-SampledGridPublicMethods-0) >>

SampledGrid(Allocator alloc): values(alloc) {} SampledGrid(pstd::span<const T> v, int nx, int ny, int nz, Allocator alloc): values(v.begin(), v.end(), alloc), nx(nx), ny(ny), nz(nz) { } int XSize() const { return nx; } int YSize() const { return ny; } int zSize() const { return nz; } const\_iterator begin() const { return values.begin(); } const\_iterator end() const { return values.end(); } template <typename F> auto Lookup([Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f) p, F convert) const { << [Compute voxel coordinates and offsets for p](#fragment-Computevoxelcoordinatesandoffsetsformonop-0) >>

[Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f) pSamples(p.x \* nx -.5f, p.y \* ny -.5f, p.z \* nz -.5f); Point3i pi = (Point3i) [Floor](https://pbr-book.org/4ed/Geometry_and_Transformations/n-Tuple_Base_Classes.html#Tuple3::Floor) (pSamples); [Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) d = pSamples - ([Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f))pi;

<< [Return trilinearly interpolated voxel values](#fragment-Returntrilinearlyinterpolatedvoxelvalues-0) >>

auto d00 = [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (d.x, Lookup(pi, convert), Lookup(pi + Vector3i(1, 0, 0), convert)); auto d10 = [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (d.x, Lookup(pi + Vector3i(0, 1, 0), convert), Lookup(pi + Vector3i(1, 1, 0), convert)); auto d01 = [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (d.x, Lookup(pi + Vector3i(0, 0, 1), convert), Lookup(pi + Vector3i(1, 0, 1), convert)); auto d11 = [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (d.x, Lookup(pi + Vector3i(0, 1, 1), convert), Lookup(pi + Vector3i(1, 1, 1), convert)); return [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (d.z, [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (d.y, d00, d10), [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (d.y, d01, d11));

} T Lookup([Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f) p) const { // Compute voxel coordinates and offsets for \_p\_ [Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f) pSamples(p.x \* nx -.5f, p.y \* ny -.5f, p.z \* nz -.5f); Point3i pi = (Point3i)Floor(pSamples); [Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) d = pSamples - ([Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f))pi; // Return trilinearly interpolated voxel values auto d00 = [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (d.x, Lookup(pi), Lookup(pi + Vector3i(1, 0, 0))); auto d10 = [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (d.x, Lookup(pi + Vector3i(0, 1, 0)), Lookup(pi + Vector3i(1, 1, 0))); auto d01 = [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (d.x, Lookup(pi + Vector3i(0, 0, 1)), Lookup(pi + Vector3i(1, 0, 1))); auto d11 = [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (d.x, Lookup(pi + Vector3i(0, 1, 1)), Lookup(pi + Vector3i(1, 1, 1))); return [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (d.z, [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (d.y, d00, d10), [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (d.y, d01, d11)); } template <typename F> auto Lookup(const Point3i &p, F convert) const { Bounds3i sampleBounds(Point3i(0, 0, 0), Point3i(nx, ny, nz)); if (!InsideExclusive(p, sampleBounds)) return convert(T{}); return convert(values\[(p.z \* ny + p.y) \* nx + p.x\]); } T Lookup(const Point3i &p) const { Bounds3i sampleBounds(Point3i(0, 0, 0), Point3i(nx, ny, nz)); if (!InsideExclusive(p, sampleBounds)) return T{}; return values\[(p.z \* ny + p.y) \* nx + p.x\]; } template <typename F> Float MaxValue(const [Bounds3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds3f) &bounds, F convert) const { [Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f) ps\[2\] = { [Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f) (bounds.pMin.x \* nx -.5f, bounds.pMin.y \* ny -.5f, bounds.pMin.z \* nz -.5f), [Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f) (bounds.pMax.x \* nx -.5f, bounds.pMax.y \* ny -.5f, bounds.pMax.z \* nz -.5f)}; Point3i pi\[2\] = {Max(Point3i(Floor(ps\[0\])), Point3i(0, 0, 0)), Min(Point3i(Floor(ps\[1\])) + Vector3i(1, 1, 1), Point3i(nx - 1, ny - 1, nz - 1))}; Float maxValue = Lookup(Point3i(pi\[0\]), convert); for (int z = pi\[0\].z; z <= pi\[1\].z; ++z) for (int y = pi\[0\].y; y <= pi\[1\].y; ++y) for (int x = pi\[0\].x; x <= pi\[1\].x; ++x) maxValue = std::max(maxValue, Lookup(Point3i(x, y, z), convert)); return maxValue; } T MaxValue(const [Bounds3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds3f) &bounds) const { return MaxValue(bounds, \[\](T value) { return value; }); } std::string ToString() const { return StringPrintf("\[ SampledGrid nx: %d ny: %d nz: %d values: %s \]", nx, ny, nz, values); }

private: << [SampledGrid Private Members](#fragment-SampledGridPrivateMembers-0) >>

pstd::vector<T> values; int nx, ny, nz;

};

It offsets a few constructors, not included here, that initialize a vector of values at specified sampling rates nx, ny, and nz in each dimension.

<<SampledGrid Private Members>>=

pstd::vector<T> values; int nx, ny, nz;

Lookup() takes a point and a function that can be used to convert from the type stored in memory to another type that is returned from the method. (This capability is used, for example, by the [RGBGridMedium](https://pbr-book.org/4ed/Volume_Scattering/Media.html#RGBGridMedium), which stores a grid of RGB values that are represented using the [RGBUnboundedSpectrum](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Color.html#RGBUnboundedSpectrum) class but then wants a corresponding [SampledSpectrum](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Representing_Spectral_Distributions.html#SampledSpectrum) at specific wavelengths to be returned from Lookup().)

<<SampledGrid Public Methods>>=

template <typename F> auto Lookup([Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f) p, F convert) const { << [Compute voxel coordinates and offsets for p](#fragment-Computevoxelcoordinatesandoffsetsformonop-0) >>

[Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f) pSamples(p.x \* nx -.5f, p.y \* ny -.5f, p.z \* nz -.5f); Point3i pi = (Point3i) [Floor](https://pbr-book.org/4ed/Geometry_and_Transformations/n-Tuple_Base_Classes.html#Tuple3::Floor) (pSamples); [Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) d = pSamples - ([Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f))pi;

<< [Return trilinearly interpolated voxel values](#fragment-Returntrilinearlyinterpolatedvoxelvalues-0) >>

auto d00 = [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (d.x, Lookup(pi, convert), Lookup(pi + Vector3i(1, 0, 0), convert)); auto d10 = [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (d.x, Lookup(pi + Vector3i(0, 1, 0), convert), Lookup(pi + Vector3i(1, 1, 0), convert)); auto d01 = [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (d.x, Lookup(pi + Vector3i(0, 0, 1), convert), Lookup(pi + Vector3i(1, 0, 1), convert)); auto d11 = [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (d.x, Lookup(pi + Vector3i(0, 1, 1), convert), Lookup(pi + Vector3i(1, 1, 1), convert)); return [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (d.z, [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (d.y, d00, d10), [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (d.y, d01, d11));

}

For the convenience of cases where the in-memory type T is the one that should be returned, a second implementation of Lookup(), not included here, provides a default identity implementation of the conversion function.

SampledGrid follows the same conventions as were used for discrete and continuous coordinates for pixel indexing, defined in Section [8.1.4](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sampling_Theory.html#sec:pixel-concepts). Here the discrete coordinates for the lower corner of the 8 samples are computed.

<<Compute voxel coordinates and offsets for p >>=

[Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f) pSamples(p.x \* nx -.5f, p.y \* ny -.5f, p.z \* nz -.5f); Point3i pi = (Point3i) [Floor](https://pbr-book.org/4ed/Geometry_and_Transformations/n-Tuple_Base_Classes.html#Tuple3::Floor) (pSamples); [Vector3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Vectors.html#Vector3f) d = pSamples - ([Point3f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point3f))pi;

A sequence of linear interpolations gives the trilinearly interpolated sample value. They use a second Lookup() method, not included here, that returns a voxel sample given integer coordinates. Out-of-bounds indices result in a default-initialized value being returned, which is generally the zero value for the type.

Note that SampledGrid is able to represent any class for which an appropriate Lerp() function is defined for the type returned by the conversion function. Further, note the use of auto, which allows this method to be implemented without worrying about what type is returned by convert.

<<Return trilinearly interpolated voxel values>>=

auto d00 = [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (d.x, Lookup(pi, convert), Lookup(pi + Vector3i(1, 0, 0), convert)); auto d10 = [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (d.x, Lookup(pi + Vector3i(0, 1, 0), convert), Lookup(pi + Vector3i(1, 1, 0), convert)); auto d01 = [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (d.x, Lookup(pi + Vector3i(0, 0, 1), convert), Lookup(pi + Vector3i(1, 0, 1), convert)); auto d11 = [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (d.x, Lookup(pi + Vector3i(0, 1, 1), convert), Lookup(pi + Vector3i(1, 1, 1), convert)); return [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (d.z, [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (d.y, d00, d10), [Lerp](https://pbr-book.org/4ed/Monte_Carlo_Integration/Sampling_Using_the_Inversion_Method.html#Lerp) (d.y, d01, d11));

Finally, the MaxValue() method, also not included here, returns a bound on the maximum value of the interpolated function over the given bounds, computed by looping over all of the sample values that contribute to grid lookups inside those bounds. It takes a function that converts the in-memory type to a Float; the maximum of all such Float s is then returned.

### B.4.6 Efficient Temporary Memory Allocations

For small objects with short lifetimes, C++’s traditional new and delete memory allocation operators may impose undesirable overhead from maintenance of their internal data structures. A custom allocation technique that has proved to be useful in such cases is *arena-based allocation*, which allocates objects from a large contiguous region of memory. In this scheme, individual objects are never explicitly freed; instead, the entire region of memory is released when the lifetime of all the allocated objects ends.

The [ScratchBuffer](#ScratchBuffer) class implements this approach. It is used for dynamic allocation of [BxDF](https://pbr-book.org/4ed/Reflection_Models/BSDF_Representation.html#BxDF) s, BSSRDF s, and [RayMajorantIterator](https://pbr-book.org/4ed/Volume_Scattering/Media.html#RayMajorantIterator) s as rays are being traced through the scene. Some of its efficiency comes from its not allowing multiple threads to use a single [ScratchBuffer](#ScratchBuffer) instance concurrently; instead, pbrt ’s [ThreadLocal](https://pbr-book.org/4ed/Utilities/Parallelism.html#ThreadLocal) capability should be used to allocate a separate [ScratchBuffer](#ScratchBuffer) for each thread that needs one.

One important detail in its definition is the use of alignas, which helps improve CPU cache performance by preventing multiple threads from accessing the same cache line. (For details, see the discussion of false sharing in Section [B.6.3](https://pbr-book.org/4ed/Utilities/Parallelism.html#sec:multi-thread-memory-perf).)

<<ScratchBuffer Definition>>=

class alignas([PBRT\_L1\_CACHE\_LINE\_SIZE](https://pbr-book.org/4ed/Utilities/Parallelism.html#PBRT_L1_CACHE_LINE_SIZE)) ScratchBuffer { public: << [ScratchBuffer Public Methods](#fragment-ScratchBufferPublicMethods-0) >>

[ScratchBuffer](#ScratchBuffer) (int size = 256): [allocSize](#ScratchBuffer::allocSize) (size) { [ptr](#ScratchBuffer::ptr) = (char \*)Allocator().[allocate\_bytes](https://pbr-book.org/4ed/Introduction/Using_and_Understanding_the_Code.html#std::pmr::polymorphic_allocator::allocate_bytes) (size, [align](#ScratchBuffer::align)); } [ScratchBuffer](#ScratchBuffer) (const [ScratchBuffer](#ScratchBuffer) &) = delete; [ScratchBuffer](#ScratchBuffer) ([ScratchBuffer](#ScratchBuffer) &&b) { [ptr](#ScratchBuffer::ptr) = b.[ptr](#ScratchBuffer::ptr); [allocSize](#ScratchBuffer::allocSize) = b.[allocSize](#ScratchBuffer::allocSize); [offset](#ScratchBuffer::offset) = b.[offset](#ScratchBuffer::offset); [smallBuffers](#ScratchBuffer::smallBuffers) = std::move(b.[smallBuffers](#ScratchBuffer::smallBuffers)); b.[ptr](#ScratchBuffer::ptr) = nullptr; b.[allocSize](#ScratchBuffer::allocSize) = b.[offset](#ScratchBuffer::offset) = 0; } ~ [ScratchBuffer](#ScratchBuffer) () { Reset(); Allocator().deallocate\_bytes([ptr](#ScratchBuffer::ptr), [allocSize](#ScratchBuffer::allocSize), [align](#ScratchBuffer::align)); } [ScratchBuffer](#ScratchBuffer) &operator=(const [ScratchBuffer](#ScratchBuffer) &) = delete; [ScratchBuffer](#ScratchBuffer) &operator=([ScratchBuffer](#ScratchBuffer) &&b) { std::swap(b.[ptr](#ScratchBuffer::ptr), [ptr](#ScratchBuffer::ptr)); std::swap(b.[allocSize](#ScratchBuffer::allocSize), [allocSize](#ScratchBuffer::allocSize)); std::swap(b.[offset](#ScratchBuffer::offset), [offset](#ScratchBuffer::offset)); std::swap(b.[smallBuffers](#ScratchBuffer::smallBuffers), [smallBuffers](#ScratchBuffer::smallBuffers)); return \*this; } void \*Alloc(size\_t size, size\_t [align](#ScratchBuffer::align)) { if (([offset](#ScratchBuffer::offset) % [align](#ScratchBuffer::align))!= 0) [offset](#ScratchBuffer::offset) += [align](#ScratchBuffer::align) - ([offset](#ScratchBuffer::offset) % [align](#ScratchBuffer::align)); if ([offset](#ScratchBuffer::offset) + size > [allocSize](#ScratchBuffer::allocSize)) [Realloc](#ScratchBuffer::Realloc) (size); void \*p = [ptr](#ScratchBuffer::ptr) + [offset](#ScratchBuffer::offset); [offset](#ScratchBuffer::offset) += size; return p; } template <typename T, typename... Args> typename AllocationTraits<T>::SingleObject Alloc(Args &&... args) { T \*p = (T \*)Alloc(sizeof(T), alignof(T)); return new (p) T(std::forward<Args>(args)...); } template <typename T> typename AllocationTraits<T>::Array Alloc(size\_t n = 1) { using ElementType = typename std::remove\_extent\_t<T>; ElementType \*ret = (ElementType \*)Alloc(n \* sizeof(ElementType), alignof(ElementType)); for (size\_t i = 0; i < n; ++i) new (&ret\[i\]) ElementType(); return ret; } void Reset() { for (const auto &buf: [smallBuffers](#ScratchBuffer::smallBuffers)) Allocator().deallocate\_bytes(buf.first, buf.second, [align](#ScratchBuffer::align)); [smallBuffers](#ScratchBuffer::smallBuffers).clear(); [offset](#ScratchBuffer::offset) = 0; }

private: << [ScratchBuffer Private Methods](#fragment-ScratchBufferPrivateMethods-0) >>

void Realloc(size\_t minSize) { [smallBuffers](#ScratchBuffer::smallBuffers).push\_back(std::make\_pair([ptr](#ScratchBuffer::ptr), [allocSize](#ScratchBuffer::allocSize))); [allocSize](#ScratchBuffer::allocSize) = std::max(2 \* minSize, [allocSize](#ScratchBuffer::allocSize) + minSize); [ptr](#ScratchBuffer::ptr) = (char \*)Allocator().[allocate\_bytes](https://pbr-book.org/4ed/Introduction/Using_and_Understanding_the_Code.html#std::pmr::polymorphic_allocator::allocate_bytes) ([allocSize](#ScratchBuffer::allocSize), [align](#ScratchBuffer::align)); [offset](#ScratchBuffer::offset) = 0; }

<< [ScratchBuffer Private Members](#fragment-ScratchBufferPrivateMembers-0) >>

static constexpr int align = [PBRT\_L1\_CACHE\_LINE\_SIZE](https://pbr-book.org/4ed/Utilities/Parallelism.html#PBRT_L1_CACHE_LINE_SIZE); char \*ptr = nullptr; int allocSize = 0, offset = 0; std::list<std::pair<char \*, size\_t>> smallBuffers;

};

The [ScratchBuffer](#ScratchBuffer) hands out pointers to memory from a single preallocated block. If the block’s size is insufficient, it will be replaced with a larger one; this allows a small default block size, though the caller can specify a larger one if the default is known to be too little.

<<ScratchBuffer Public Methods>>=

[ScratchBuffer](#ScratchBuffer) (int size = 256): [allocSize](#ScratchBuffer::allocSize) (size) { [ptr](#ScratchBuffer::ptr) = (char \*)Allocator().[allocate\_bytes](https://pbr-book.org/4ed/Introduction/Using_and_Understanding_the_Code.html#std::pmr::polymorphic_allocator::allocate_bytes) (size, [align](#ScratchBuffer::align)); }

offset maintains the offset after ptr where free memory begins.

<<ScratchBuffer Private Members>>=

static constexpr int align = [PBRT\_L1\_CACHE\_LINE\_SIZE](https://pbr-book.org/4ed/Utilities/Parallelism.html#PBRT_L1_CACHE_LINE_SIZE); char \*ptr = nullptr; int allocSize = 0, offset = 0;

To service an allocation request, the allocation routine first advances offset as necessary so that the returned address meets the specified memory alignment. (It is thus required that ptr has at minimum that alignment.) If the allocation would go past the end of the allocated buffer, Realloc() takes care of allocating a new, larger buffer. With the usual case of long-lived ScratchBuffer s, this should happen rarely. Given sufficient space, the pointer can be returned and offset incremented to account for the allocation.

void \*Alloc(size\_t size, size\_t align) { if (([offset](#ScratchBuffer::offset) % align)!= 0) [offset](#ScratchBuffer::offset) += align - ([offset](#ScratchBuffer::offset) % align); if ([offset](#ScratchBuffer::offset) + size > [allocSize](#ScratchBuffer::allocSize)) [Realloc](#ScratchBuffer::Realloc) (size); void \*p = [ptr](#ScratchBuffer::ptr) + [offset](#ScratchBuffer::offset); [offset](#ScratchBuffer::offset) += size; return p; }

ScratchBuffer provides two additional Alloc() methods that are not included here. Both are templated on the type of object being allocated. One allocates a single object, passing along provided parameters to its constructor. The other allocates an array of objects of a specified length, running the default constructor for each one.

If a larger buffer is needed, Realloc() holds on to a pointer to the current buffer and its size in smallBuffers. The current buffer cannot be freed until the user later calls ScratchBuffer ’s Reset() method, but it should be returned to the system then, as ScratchBuffer will henceforth have no need for it.

<<ScratchBuffer Private Methods>>=

void Realloc(size\_t minSize) { [smallBuffers](#ScratchBuffer::smallBuffers).push\_back(std::make\_pair([ptr](#ScratchBuffer::ptr), [allocSize](#ScratchBuffer::allocSize))); [allocSize](#ScratchBuffer::allocSize) = std::max(2 \* minSize, [allocSize](#ScratchBuffer::allocSize) + minSize); [ptr](#ScratchBuffer::ptr) = (char \*)Allocator().[allocate\_bytes](https://pbr-book.org/4ed/Introduction/Using_and_Understanding_the_Code.html#std::pmr::polymorphic_allocator::allocate_bytes) ([allocSize](#ScratchBuffer::allocSize), [align](#ScratchBuffer::align)); [offset](#ScratchBuffer::offset) = 0; }

<<ScratchBuffer Private Members>>+=

std::list<std::pair<char \*, size\_t>> smallBuffers;

A call to Reset() is lightweight, usually just resetting offset to 0. Note that, lacking the necessary information to be able to do so, it does not run the destructors of the allocated objects.

<<ScratchBuffer Public Methods>>+=

void Reset() { for (const auto &buf: [smallBuffers](#ScratchBuffer::smallBuffers)) Allocator().deallocate\_bytes(buf.first, buf.second, align); [smallBuffers](#ScratchBuffer::smallBuffers).clear(); [offset](#ScratchBuffer::offset) = 0; }