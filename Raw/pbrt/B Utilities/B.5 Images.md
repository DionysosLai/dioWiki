---
title: "Images"
source: "https://pbr-book.org/4ed/Utilities/Images"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
## B.5 Images
> ## B.5 图像

The Image class stores a 2D array of pixel values, where each pixel stores a fixed number of scalar-valued *channels*.
> Image 类存储像素值的二维数组，其中每个像素存储固定数量的标量值*通道*。 (For example, an image storing RGB color would have three channels.) It provides a variety of operations ranging from looking up or interpolating pixel values to image-wide operations like resizing. It is at the core of both the [FloatImageTexture](https://pbr-book.org/4ed/Textures_and_Materials/Image_Texture.html#FloatImageTexture) and [SpectrumImageTexture](https://pbr-book.org/4ed/Textures_and_Materials/Image_Texture.html#SpectrumImageTexture) classes and is used for lights such as the [ImageInfiniteLight](https://pbr-book.org/4ed/Light_Sources/Infinite_Area_Lights.html#ImageInfiniteLight) and [ProjectionLight](https://pbr-book.org/4ed/Light_Sources/Point_Lights.html#ProjectionLight). Furthermore, both of pbrt ’s [Film](https://pbr-book.org/4ed/Cameras_and_Film/Film_and_Imaging.html#Film) implementations make use of its capabilities for writing images to disk in a variety of file formats.

<<Image Definition>>=

class Image { public: << [Image Public Methods](#fragment-ImagePublicMethods-0) >>

[Image](#Image) ([Allocator](https://pbr-book.org/4ed/Introduction/Using_and_Understanding_the_Code.html#Allocator) alloc = {}): p8(alloc), p16(alloc), p32(alloc), [format](#Image::format) (PixelFormat::U256), [resolution](#Image::resolution) (0, 0) {} [Image](#Image) (pstd::vector<uint8\_t> p8, [Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) [resolution](#Image::resolution), pstd::span<const std::string> channels, [ColorEncoding](#ColorEncoding) [encoding](#Image::encoding)); [Image](#Image) (pstd::vector<Half> p16, [Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) [resolution](#Image::resolution), pstd::span<const std::string> channels); [Image](#Image) (pstd::vector<float> p32, [Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) [resolution](#Image::resolution), pstd::span<const std::string> channels); [Image](#Image) (PixelFormat [format](#Image::format), [Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) [resolution](#Image::resolution), pstd::span<const std::string> [channelNames](#Image::channelNames), [ColorEncoding](#ColorEncoding) [encoding](#Image::encoding) = nullptr, [Allocator](https://pbr-book.org/4ed/Introduction/Using_and_Understanding_the_Code.html#Allocator) alloc = {}); PixelFormat Format() const { return [format](#Image::format); } [Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) Resolution() const { return [resolution](#Image::resolution); } int [NChannels](#Image::NChannels) () const { return [channelNames](#Image::channelNames).size(); } std::vector<std::string> ChannelNames() const; const [ColorEncoding](#ColorEncoding) Encoding() const { return [encoding](#Image::encoding); } operator bool() const { return [resolution](#Image::resolution).x > 0 && [resolution](#Image::resolution).y > 0; } size\_t PixelOffset([Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) p) const { return [NChannels](#Image::NChannels) () \* (p.y \* [resolution](#Image::resolution).x + p.x); } Float GetChannel([Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) p, int c, WrapMode2D wrapMode = WrapMode::[Clamp](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Clamp)) const { << [Remap provided pixel coordinates before reading channel](#fragment-Remapprovidedpixelcoordinatesbeforereadingchannel-0) >>

if (!RemapPixelCoords(&p, resolution, wrapMode)) return 0;

switch ([format](#Image::format)) { case PixelFormat::U256: { << [Return U256 -encoded pixel channel value](#fragment-ReturnmonoU256-encodedpixelchannelvalue-0) >>

Float r; encoding.[ToLinear](#ColorEncoding::ToLinear) ({& [p8](#Image::p8) \[[PixelOffset](#Image::PixelOffset) (p) + c\], 1}, {&r, 1}); return r;

} case PixelFormat::Half: { << [Return Half -encoded pixel channel value](#fragment-ReturnmonoHalf-encodedpixelchannelvalue-0) >>

return Float([p16](#Image::p16) \[[PixelOffset](#Image::PixelOffset) (p) + c\]);

} case PixelFormat::Float: { << [Return Float -encoded pixel channel value](#fragment-ReturnmonoFloat-encodedpixelchannelvalue-0) >>

return [p32](#Image::p32) \[[PixelOffset](#Image::PixelOffset) (p) + c\];

} } } Float BilerpChannel([Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) p, int c, WrapMode2D wrapMode = WrapMode::[Clamp](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Clamp)) const { << [Compute discrete pixel coordinates and offsets for p](#fragment-Computediscretepixelcoordinatesandoffsetsformonop-0) >>

Float x = p\[0\] \* resolution.x - 0.5f, y = p\[1\] \* resolution.y - 0.5f; int xi = pstd::floor(x), yi = pstd::floor(y); Float dx = x - xi, dy = y - yi;

<< [Load pixel channel values and return bilinearly interpolated value](#fragment-Loadpixelchannelvaluesandreturnbilinearlyinterpolatedvalue-0) >>

pstd::array<Float, 4> v = { [GetChannel](#Image::GetChannel) ({xi, yi}, c, wrapMode), [GetChannel](#Image::GetChannel) ({xi + 1, yi}, c, wrapMode), [GetChannel](#Image::GetChannel) ({xi, yi + 1}, c, wrapMode), [GetChannel](#Image::GetChannel) ({xi + 1, yi + 1}, c, wrapMode)}; return ((1 - dx) \* (1 - dy) \* v\[0\] + dx \* (1 - dy) \* v\[1\] + (1 - dx) \* dy \* v\[2\] + dx \* dy \* v\[3\]);

} void SetChannel([Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) p, int c, Float value); ImageChannelValues GetChannels([Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) p, WrapMode2D wrapMode = WrapMode::[Clamp](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Clamp)) const; ImageChannelDesc GetChannelDesc( pstd::span<const std::string> channels) const; ImageChannelDesc AllChannelsDesc() const { ImageChannelDesc desc; desc.offset.resize([NChannels](#Image::NChannels) ()); for (int i = 0; i < [NChannels](#Image::NChannels) (); ++i) desc.offset\[i\] = i; return desc; } ImageChannelValues GetChannels([Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) p, const ImageChannelDesc &desc, WrapMode2D wrapMode = WrapMode::[Clamp](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Clamp)) const; [Image](#Image) SelectChannels(const ImageChannelDesc &desc, [Allocator](https://pbr-book.org/4ed/Introduction/Using_and_Understanding_the_Code.html#Allocator) alloc = {}) const; [Image](#Image) Crop(const [Bounds2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2i) &bounds, [Allocator](https://pbr-book.org/4ed/Introduction/Using_and_Understanding_the_Code.html#Allocator) alloc = {}) const; void CopyRectOut(const [Bounds2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2i) &extent, pstd::span<float> buf, WrapMode2D wrapMode = WrapMode::[Clamp](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Clamp)) const; void CopyRectIn(const [Bounds2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2i) &extent, pstd::span<const float> buf); ImageChannelValues Average(const ImageChannelDesc &desc) const; bool HasAnyInfinitePixels() const; bool HasAnyNaNPixels() const; ImageChannelValues MAE(const ImageChannelDesc &desc, const [Image](#Image) &ref, [Image](#Image) \*errorImage = nullptr) const; ImageChannelValues MSE(const ImageChannelDesc &desc, const [Image](#Image) &ref, [Image](#Image) \*mseImage = nullptr) const; ImageChannelValues MRSE(const ImageChannelDesc &desc, const [Image](#Image) &ref, [Image](#Image) \*mrseImage = nullptr) const; [Image](#Image) GaussianFilter(const ImageChannelDesc &desc, int halfWidth, Float sigma) const; template <typename F> [Array2D](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D) <Float> GetSamplingDistribution( F dxdA, const [Bounds2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2f) &domain = [Bounds2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2f) ([Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) (0, 0), [Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) (1, 1)), [Allocator](https://pbr-book.org/4ed/Introduction/Using_and_Understanding_the_Code.html#Allocator) alloc = {}); [Array2D](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D) <Float> GetSamplingDistribution() { return GetSamplingDistribution(\[\]([Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f)) { return Float(1); }); } static ImageAndMetadata Read(std::string filename, [Allocator](https://pbr-book.org/4ed/Introduction/Using_and_Understanding_the_Code.html#Allocator) alloc = {}, [ColorEncoding](#ColorEncoding) [encoding](#Image::encoding) = nullptr); bool Write(std::string name, const ImageMetadata &metadata = {}) const; [Image](#Image) ConvertToFormat(PixelFormat [format](#Image::format), [ColorEncoding](#ColorEncoding) [encoding](#Image::encoding) = nullptr) const; // TODO? provide an iterator to iterate over all pixels and channels? PBRT\_CPU\_GPU Float LookupNearestChannel([Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) p, int c, WrapMode2D wrapMode = WrapMode::[Clamp](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Clamp)) const { [Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) pi(p.x \* [resolution](#Image::resolution).x, p.y \* [resolution](#Image::resolution).y); return GetChannel(pi, c, wrapMode); } ImageChannelValues LookupNearest([Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) p, WrapMode2D wrapMode = WrapMode::[Clamp](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Clamp)) const; ImageChannelValues LookupNearest([Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) p, const ImageChannelDesc &desc, WrapMode2D wrapMode = WrapMode::[Clamp](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Clamp)) const; ImageChannelValues Bilerp([Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) p, WrapMode2D wrapMode = WrapMode::[Clamp](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Clamp)) const; ImageChannelValues Bilerp([Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) p, const ImageChannelDesc &desc, WrapMode2D wrapMode = WrapMode::[Clamp](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Clamp)) const; void SetChannels([Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) p, const ImageChannelValues &values); void SetChannels([Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) p, pstd::span<const Float> values); void SetChannels([Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) p, const ImageChannelDesc &desc, pstd::span<const Float> values); [Image](#Image) FloatResizeUp([Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) newResolution, WrapMode2D wrap) const; void FlipY(); static pstd::vector< [Image](#Image) > GeneratePyramid([Image](#Image) image, WrapMode2D wrapMode, [Allocator](https://pbr-book.org/4ed/Introduction/Using_and_Understanding_the_Code.html#Allocator) alloc = {}); std::vector<std::string> ChannelNames(const ImageChannelDesc &) const; PBRT\_CPU\_GPU size\_t BytesUsed() const { return p8.size() + 2 \* p16.size() + 4 \* p32.size(); } PBRT\_CPU\_GPU const void \*RawPointer([Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) p) const { if (Is8Bit([format](#Image::format))) return p8.data() + PixelOffset(p); if (Is16Bit([format](#Image::format))) return p16.data() + PixelOffset(p); else { CHECK(Is32Bit([format](#Image::format))); return p32.data() + PixelOffset(p); } } PBRT\_CPU\_GPU void \*RawPointer([Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) p) { return const\_cast<void \*>(((const [Image](#Image) \*)this)->RawPointer(p)); } [Image](#Image) JointBilateralFilter(const ImageChannelDesc &toFilter, int halfWidth, const Float xySigma\[2\], const ImageChannelDesc &joint, const ImageChannelValues &jointSigma) const; std::string ToString() const;

private: <<Image Private Methods>>

static std::vector<ResampleWeight> ResampleWeights(int oldRes, int newRes); bool WriteEXR(const std::string &name, const ImageMetadata &metadata) const; bool WritePFM(const std::string &name, const ImageMetadata &metadata) const; bool WritePNG(const std::string &name, const ImageMetadata &metadata) const;

<< [Image Private Members](#fragment-ImagePrivateMembers-0) >>

PixelFormat format; [Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) resolution; pstd::vector<std::string> channelNames; [ColorEncoding](#ColorEncoding) encoding = nullptr; pstd::vector<uint8\_t> p8; pstd::vector<Half> p16; pstd::vector<float> p32;

};

Image is defined in the files [util/image.h](https://github.com/mmp/pbrt-v4/tree/master/src/pbrt/util/image.h) and [util/image.cpp](https://github.com/mmp/pbrt-v4/tree/master/src/pbrt/util/image.cpp).

The Image class provides a number of constructors as well as a method (which will be discussed in Section [B.5.3](#sec:image-read-write)) that reads an image from a file. We will only describe the operation of its most general-purpose constructor here; see the class definition for the remainder of them.

This Image constructor takes the in-memory format to use for storing pixel data, format, the overall image resolution, and names for all of the channels. Optionally, both a [ColorEncoding](#ColorEncoding) and an [Allocator](https://pbr-book.org/4ed/Introduction/Using_and_Understanding_the_Code.html#Allocator) can be provided; the former specifies a technique for encoding fixed-precision pixel values and will be discussed in Section [B.5.6](#sec:color-encodings).

<<Image Public Methods>>=

[Image](#Image) (PixelFormat format, [Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) resolution, pstd::span<const std::string> channelNames, [ColorEncoding](#ColorEncoding) encoding = nullptr, [Allocator](https://pbr-book.org/4ed/Introduction/Using_and_Understanding_the_Code.html#Allocator) alloc = {});

Three in-memory formats are supported for pixel channel values. Note that [Image](#Image) uses the same encoding for all channels; it is not possible to mix and match. The first of them, U256, specifies an unsigned 8-bit encoding of values between 0 and 1 using integers ranging from 0 to 255. This is a memory-efficient encoding and is widely used in image file formats, but it provides limited range. Half uses 16-bit floating-point values (which were described in Section [6.8.1](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#sec:ieee-fp)) to provide much more dynamic range than U256, while still being memory efficient. Finally, Float specifies full 32-bit float s. It would not be difficult to generalize Image to also support double-precision floating-point storage, though we have not found a need to do so for pbrt ’s uses of this class.

<<PixelFormat Definition>>=

enum class PixelFormat { U256, Half, Float };

A few helper functions test whether a given PixelFormat uses a specified amount of storage. Isolating these tests in this way makes it easier, for example, to extend Image to also provide a 16-bit integer representation without needing to update logic that purely relates to memory allocation.

<<PixelFormat Inline Functions>>=

bool Is8Bit([PixelFormat](#PixelFormat::Float) format) { return format == [PixelFormat](#PixelFormat::Float)::U256; } bool Is16Bit([PixelFormat](#PixelFormat::Float) format) { return format == [PixelFormat](#PixelFormat::Float)::Half; } bool Is32Bit([PixelFormat](#PixelFormat::Float) format) { return format == [PixelFormat](#PixelFormat::Float)::Float; }

The size of the provided channelNames parameter determines the number of channels the image stores at each pixel. The Image class does not impose any semantics on the channels or attempt to interpret their meaning but instead just stores values and performs the operations on them specified by the caller.

<<Image Private Members>>=

PixelFormat format; [Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) resolution; pstd::vector<std::string> channelNames; [ColorEncoding](#ColorEncoding) encoding = nullptr;

Because these values are stored as private member variables, Image provides corresponding accessor methods.

PixelFormat Format() const { return [format](#Image::format); } [Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) Resolution() const { return [resolution](#Image::resolution); } int NChannels() const { return [channelNames](#Image::channelNames).size(); } std::vector<std::string> ChannelNames() const; const [ColorEncoding](#ColorEncoding) Encoding() const { return [encoding](#Image::encoding); }

Image allows the specification of an image with no pixels; operator bool provides a quick check for whether an image is nonempty.

operator bool() const { return [resolution](#Image::resolution).x > 0 && [resolution](#Image::resolution).y > 0; }

One of the following member variables stores the pixel values. Which one is used is determined by the specified [PixelFormat](#PixelFormat).

<<Image Private Members>>+=

pstd::vector<uint8\_t> p8; pstd::vector<Half> p16; pstd::vector<float> p32;

The PixelOffset() method returns the offset into the pixel value array for given integer pixel coordinates. In debug builds, a [DCHECK()](https://pbr-book.org/4ed/Utilities/User_Interaction.html#DCHECK) call, not included here, checks that the provided coordinates are between 0 and the image resolution in each dimension.

A few factors determine the following indexing computation: first, the coordinate system for images has at the upper left corner of the image; images are then laid out in scanline order, and each pixel’s channel values are laid out successively in memory.

size\_t PixelOffset([Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) p) const { return [NChannels](#Image::NChannels) () \* (p.y \* [resolution](#Image::resolution).x + p.x); }

An alternative memory layout would first store all the pixels’ first channel values contiguously in memory, then the second channel values, and so forth. In pbrt, the most common uses of Image involve accessing all the channels in a pixel, so the layout we have chosen gives better memory access coherence, which generally leads to better cache performance.

### B.5.1 Working with Pixel Values

The GetChannel() method returns the floating-point value for a single image channel, taking care of both addressing pixels and converting the in-memory value to a Float. Note that if this method is used, it is the caller’s responsibility to keep track of what is being stored in each channel.

Float GetChannel([Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) p, int c, WrapMode2D wrapMode = WrapMode::[Clamp](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Clamp)) const { << [Remap provided pixel coordinates before reading channel](#fragment-Remapprovidedpixelcoordinatesbeforereadingchannel-0) >>

if (!RemapPixelCoords(&p, resolution, wrapMode)) return 0;

switch (format) { case PixelFormat::U256: { << [Return U256 -encoded pixel channel value](#fragment-ReturnmonoU256-encodedpixelchannelvalue-0) >>

Float r; encoding.[ToLinear](#ColorEncoding::ToLinear) ({& [p8](#Image::p8) \[[PixelOffset](#Image::PixelOffset) (p) + c\], 1}, {&r, 1}); return r;

} case PixelFormat::Half: { << [Return Half -encoded pixel channel value](#fragment-ReturnmonoHalf-encodedpixelchannelvalue-0) >>

return Float([p16](#Image::p16) \[[PixelOffset](#Image::PixelOffset) (p) + c\]);

} case PixelFormat::Float: { << [Return Float -encoded pixel channel value](#fragment-ReturnmonoFloat-encodedpixelchannelvalue-0) >>

return [p32](#Image::p32) \[[PixelOffset](#Image::PixelOffset) (p) + c\];

} } }

Like all the upcoming methods that return pixel values, the lookup point p that is passed to GetChannel() is not required to be inside the image bounds. This is a convenience for code that calls these methods and saves them from all needing to handle boundary conditions themselves.

WrapMode and WrapMode2D specify how out-of-bounds coordinates should be handled. The first three options are widely used in texture mapping, and are respectively to return a black (zero-valued) result, to clamp out-of-bounds coordinates to the valid bounds, and to take them modulus the image resolution, which effectively repeats the image infinitely. The last option, OctahedralSphere, accounts for the layout of the octahedron used in the definition of equi-area spherical mapping (see Section [3.8.3](https://pbr-book.org/4ed/Geometry_and_Transformations/Spherical_Geometry.html#sec:equi-area-sphere)) and should be used when looking up values in images that are based on that parameterization.

<<WrapMode Definitions>>=

enum class WrapMode { Black, [Clamp](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Clamp), Repeat, OctahedralSphere }; struct WrapMode2D { pstd::array<WrapMode, 2> wrap; };

The RemapPixelCoords() function handles modifying the pixel coordinates as needed according to the WrapMode for each dimension. If an out-of-bounds coordinate has been provided and [WrapMode::Black](#WrapMode::Black) has been specified, it returns a false value, which is handled here by returning 0. The implementation of this function is not included here.

<<Remap provided pixel coordinates before reading channel>>=

if (!RemapPixelCoords(&p, resolution, wrapMode)) return 0;

Given a valid pixel coordinate, PixelOffset() gives the offset to the first channel for that pixel. A further offset by the channel index c is all that is left to get to the channel value. For U256 images, this value is decoded into a Float using the specified color encoding (discussed in Section [B.5.6](#sec:color-encodings)).

<<Return U256 -encoded pixel channel value>>=

Float r; encoding.[ToLinear](#ColorEncoding::ToLinear) ({& [p8](#Image::p8) \[[PixelOffset](#Image::PixelOffset) (p) + c\], 1}, {&r, 1}); return r;

For Half images, the [Half](https://pbr-book.org/4ed/Shapes/Managing_Rounding_Error.html#Half) class’s Float conversion operator is invoked to get the return value.

<<Return Half -encoded pixel channel value>>=

return Float([p16](#Image::p16) \[[PixelOffset](#Image::PixelOffset) (p) + c\]);

And for Float images, the task is trivial.

<<Return Float -encoded pixel channel value>>=

return [p32](#Image::p32) \[[PixelOffset](#Image::PixelOffset) (p) + c\];

The Image class also provides a LookupNearestChannel() method, which returns the specified channel value for the pixel sample nearest a provided coordinate with respect to. It is a simple wrapper around GetChannel(), so it is not included here.

Slightly more interesting in its implementation is BilerpChannel, which uses bilinear interpolation between four image pixels to compute the channel value. (This is equivalent to filtering with a pixel-wide triangle filter.)

Float BilerpChannel([Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) p, int c, WrapMode2D wrapMode = WrapMode::[Clamp](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Clamp)) const { << [Compute discrete pixel coordinates and offsets for p](#fragment-Computediscretepixelcoordinatesandoffsetsformonop-0) >>

Float x = p\[0\] \* resolution.x - 0.5f, y = p\[1\] \* resolution.y - 0.5f; int xi = pstd::floor(x), yi = pstd::floor(y); Float dx = x - xi, dy = y - yi;

<< [Load pixel channel values and return bilinearly interpolated value](#fragment-Loadpixelchannelvaluesandreturnbilinearlyinterpolatedvalue-0) >>

pstd::array<Float, 4> v = { [GetChannel](#Image::GetChannel) ({xi, yi}, c, wrapMode), [GetChannel](#Image::GetChannel) ({xi + 1, yi}, c, wrapMode), [GetChannel](#Image::GetChannel) ({xi, yi + 1}, c, wrapMode), [GetChannel](#Image::GetChannel) ({xi + 1, yi + 1}, c, wrapMode)}; return ((1 - dx) \* (1 - dy) \* v\[0\] + dx \* (1 - dy) \* v\[1\] + (1 - dx) \* dy \* v\[2\] + dx \* dy \* v\[3\]);

}

The first step is to scale the provided coordinates p by the image resolution, turning them into continuous pixel coordinates. Because these are continuous coordinates and the pixels in the image are defined at discrete pixel coordinates, it is important to carefully convert into a common representation (Section [8.1.4](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Sampling_Theory.html#sec:pixel-concepts)). Here, the work is performed using discrete coordinates, with the continuous pixel coordinates mapped to the discrete space.

For example, consider the 1D case with a continuous texture coordinate of: this coordinate is a distance of below the discrete texel coordinate (which corresponds to a continuous coordinate of ) and is above the discrete coordinate (continuous coordinate ). Thus, if we subtract from the continuous coordinate, giving, we can correctly compute the correct distances to the discrete coordinates and by subtracting.

<<Compute discrete pixel coordinates and offsets for p >>=

Float x = p\[0\] \* resolution.x - 0.5f, y = p\[1\] \* resolution.y - 0.5f; int xi = pstd::floor(x), yi = pstd::floor(y); Float dx = x - xi, dy = y - yi;

After the distances are found in each dimension to the pixel at the last integer before the given coordinates, dx and dy, the four pixels are bilinearly interpolated.

<<Load pixel channel values and return bilinearly interpolated value>>=

pstd::array<Float, 4> v = { [GetChannel](#Image::GetChannel) ({xi, yi}, c, wrapMode), [GetChannel](#Image::GetChannel) ({xi + 1, yi}, c, wrapMode), [GetChannel](#Image::GetChannel) ({xi, yi + 1}, c, wrapMode), [GetChannel](#Image::GetChannel) ({xi + 1, yi + 1}, c, wrapMode)}; return ((1 - dx) \* (1 - dy) \* v\[0\] + dx \* (1 - dy) \* v\[1\] + (1 - dx) \* dy \* v\[2\] + dx \* dy \* v\[3\]);

The SetChannel() method, the implementation of which is not included in the book, sets the value of a channel in a specified pixel.

void SetChannel([Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) p, int c, Float value);

A few methods return multiple pixel channel values all at once. Doing so can be more efficient than repeatedly calling methods like GetChannel() or BilerpChannel(), as various common computations like handling the wrapMode can be done just once.

GetChannels() returns all the channel values for a given pixel all at once. (There are also LookupNearest() and Bilerp() methods that similarly perform the corresponding lookup on all channels and return the result using ImageChannelValues.)

ImageChannelValues GetChannels([Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) p, WrapMode2D wrapMode = WrapMode::[Clamp](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Clamp)) const;

GetChannels() returns the channel values using an instance of the ImageChannelValues class, the definition of which is not included here. ImageChannelValues can be operated on more or less as if it were a std::vector, though it is based on the [InlinedVector](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#InlinedVector) class that was described in Section [B.4](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#sec:containers). It is thus able to avoid the cost of the dynamic memory allocations that std::vector would otherwise require if a small number of channel values were being returned.

It is also possible to specify a particular subset of the channels for these sorts of operations. GetChannelDesc() takes one or more image channel names and returns an instance of the ImageChannelDesc class. This class is opaque to the caller, but tracks which channel index each of the requested channels corresponds to. It includes an operator bool() method that can be called to check whether the requested channels were in fact present in the image.

ImageChannelDesc GetChannelDesc( pstd::span<const std::string> channels) const;

All the methods that we have seen in this section also have variants that take an ImageChannelDesc and then return values for just the specified channels, in the order they were requested in the call to GetChannelDesc(). Here is the one for GetChannels():

ImageChannelValues GetChannels([Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) p, const ImageChannelDesc &desc, WrapMode2D wrapMode = WrapMode::[Clamp](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Clamp)) const;

### B.5.2 Image-Wide Operations

The Image class also provides a number of operations that operate on the entire image, again agnostic to the semantics of the values an image stores.

SelectChannels() returns a new image that includes only the specified channels of the original image, and Crop() returns an image that contains the specified subset of pixels of the original.

[Image](#Image) SelectChannels(const ImageChannelDesc &desc, Allocator alloc = {}) const; [Image](#Image) Crop(const [Bounds2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2i) &bounds, Allocator alloc = {}) const;

CopyRectOut() and CopyRectIn() copy the specified rectangular regions of the image to and from the provided buffers. For some performance-sensitive image processing operations, it is helpful to incur the overhead of converting the in-memory image format to float s just once so that subsequent operations can operate directly on float values.

void CopyRectOut(const [Bounds2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2i) &extent, pstd::span<float> buf, WrapMode2D wrapMode = WrapMode::[Clamp](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Clamp)) const; void CopyRectIn(const [Bounds2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2i) &extent, pstd::span<const float> buf);

A number of methods compute aggregate statistics about the image. Average() returns the average value of each specified channel across the entire image.

ImageChannelValues Average(const ImageChannelDesc &desc) const;

Two methods respectively check for pixels with infinite or not-a-number values.

bool HasAnyInfinitePixels() const; bool HasAnyNaNPixels() const;

Three methods measure error, comparing the image to a provided reference image, which should have the same resolution and named channels. Each takes a set of channels to include in the error computation and returns the error with respect to the specified metric. Optionally, they return an Image where each pixel stores its error.

MAE() computes mean absolute error—the absolute value of the difference with the reference image. MSE() computes mean squared error, and MRSE() computes mean relative squared error, which is based on dividing the squared error by the reference value.

ImageChannelValues MAE(const ImageChannelDesc &desc, const [Image](#Image) &ref, [Image](#Image) \*errorImage = nullptr) const; ImageChannelValues MSE(const ImageChannelDesc &desc, const [Image](#Image) &ref, [Image](#Image) \*mseImage = nullptr) const; ImageChannelValues MRSE(const ImageChannelDesc &desc, const [Image](#Image) &ref, [Image](#Image) \*mrseImage = nullptr) const;

Finally, GetSamplingDistribution() returns a 2D array of scalar weights for use in importance sampling. The weights are not normalized, but are suitable to be directly passed to the [PiecewiseConstant2D](https://pbr-book.org/4ed/Sampling_Algorithms/Sampling_Multidimensional_Functions.html#PiecewiseConstant2D) class’s constructor. The caller can optionally specify the domain of the image as well as a function that returns a change of variables factor if the final sampling domain is not uniform and over. This factor is then included in the sampling distribution.

template <typename F> [Array2D](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D) <Float> GetSamplingDistribution( F dxdA, const [Bounds2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2f) &domain = [Bounds2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2f) ([Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) (0, 0), [Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f) (1, 1)), Allocator alloc = {}); [Array2D](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#Array2D) <Float> GetSamplingDistribution() { return GetSamplingDistribution(\[\]([Point2f](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2f)) { return Float(1); }); }

### B.5.3 Reading and Writing Images

Many image file formats have been developed over the years and it is worthwhile to support a variety of them, especially for the convenience of scene specification. pbrt is able to read a variety of other image formats, including JPG, TGA, BMP, GIF, PFM, HDR, and OpenEXR.

For pbrt ’s image output requirements, we are mainly interested in those that support imagery represented by floating-point pixel values. In particular, the images generated by pbrt will often have a large dynamic range; such formats are crucial for being able to store the computed radiance values directly. Legacy image file formats that store 8 bits of data for red, green, and blue components to represent colors in the range are not a good fit for physically based rendering.

pbrt supports reading and writing two floating-point image file formats: OpenEXR and PFM. (Support for both reading and writing PNGs is also provided, though that format has limited dynamic range.) OpenEXR is a floating-point file format originally designed at Industrial Light and Magic for use in movie productions ([Kainz et al. 2004](https://pbr-book.org/4ed/Utilities/Further_Reading.html#cite:Kainz04)). We chose this format because it has a clean design, is easy to use, and has first-class support for floating-point image data. Libraries that read and write OpenEXR images are freely available, and support for the format is available in many other tools.

PFM is a floating-point format based on an extension to the PPM file format; it is very easily read and written, though it is not as widely supported as OpenEXR. Unlike OpenEXR, it does not support compression, so files may be fairly large.

The [Image](#Image) Read() method attempts to read an image from the given file. It uses the suffix at the end of the filename to determine which image file format reader to use.

static ImageAndMetadata Read(std::string filename, Allocator alloc = {}, [ColorEncoding](#ColorEncoding) encoding = nullptr);

Image::Read() returns an instance of the ImageAndMetadata structure. In the event of an error reading the image, it issues an error message and exits immediately, so no error handling is required of the caller.

<<ImageAndMetadata Definition>>=

struct ImageAndMetadata { [Image](#Image) image; ImageMetadata metadata; };

Some image formats can store additional metadata beyond the pixel values; ImageMetadata is pbrt ’s container for this information. OpenEXR is particularly flexible in this regard: the user is free to add arbitrary named metadata using a variety of data types.

<<ImageMetadata Definition>>=

struct ImageMetadata { <<ImageMetadata Public Methods>>

const [RGBColorSpace](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Color.html#RGBColorSpace) \*GetColorSpace() const;

<< [ImageMetadata Public Members](#fragment-ImageMetadataPublicMembers-0) >>

pstd::optional<float> renderTimeSeconds; pstd::optional< [SquareMatrix](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix) <4>> cameraFromWorld, NDCFromWorld; pstd::optional< [Bounds2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2i) > pixelBounds; pstd::optional< [Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) > fullResolution; pstd::optional<int> samplesPerPixel; pstd::optional<const [RGBColorSpace](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Color.html#RGBColorSpace) \*> colorSpace;

};

If the image has the corresponding metadata, pbrt ’s image reading routines initialize the following fields.

<<ImageMetadata Public Members>>=

pstd::optional<float> renderTimeSeconds; pstd::optional< [SquareMatrix](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#SquareMatrix) <4>> cameraFromWorld, NDCFromWorld; pstd::optional< [Bounds2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2i) > pixelBounds; pstd::optional< [Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) > fullResolution; pstd::optional<int> samplesPerPixel; pstd::optional<const [RGBColorSpace](https://pbr-book.org/4ed/Radiometry,_Spectra,_and_Color/Color.html#RGBColorSpace) \*> colorSpace;

The Write() method writes an image in one of the supported formats, based on the extension of the filename passed to it. It stores as much of the provided metadata as possible given the image format used.

<<Image Public Methods>>+=

bool Write(std::string name, const ImageMetadata &metadata = {}) const;

### B.5.4 Resizing Images

Image resizing involves application of the sampling and reconstruction theory from Chapter [8](https://pbr-book.org/4ed/Sampling_and_Reconstruction.html#chap:sampling-reconstruction): we have an image function that has been sampled at one sampling rate, and we would like to reconstruct a continuous image function from the original samples to resample at a new set of sample positions. In this section, we will discuss the Image ’s FloatResizeUp() method, which resamples an image to a higher resolution. Because this represents an increase in the sampling rate from the original rate, we do not have to worry about introducing aliasing due to undersampling high-frequency components in this step; we only need to reconstruct and directly resample the new function. Figure [B.6](#fig:texture-magnify-resample) illustrates this task in 1D.

[![](https://pbr-book.org/4ed/Utilities/phabbf06.svg)](https://pbr-book.org/4ed/Utilities/phabbf06.svg)

Figure B.6: To increase an image’s resolution, the [Image](#Image) class performs two 1D resampling steps with a separable reconstruction filter. (a) A 1D function reconstructed from four samples, denoted by dots. (b) To represent the same image function with more samples, we only need to reconstruct the continuous function and evaluate it at the new positions.

A separable reconstruction filter is used for this task; recall from Section [8.8](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Image_Reconstruction.html#sec:image-reconstruction) that separable filters can be written as the product of 1D filters:. One advantage of a separable filter is that if we are using one to resample an image from one resolution to another, then we can implement the resampling as two 1D resampling steps, first resampling in to create an image of resolution and then resampling that image to create the final image of resolution. Resampling the image via two 1D steps in this manner simplifies implementation and makes the number of pixels accessed for each pixel in the final image a linear function of the filter width, rather than a quadratic one.

Reconstructing the original image function and sampling it at a new pixel’s position are mathematically equivalent to centering the reconstruction filter kernel at the new pixel’s position and weighting the nearby pixels in the original image appropriately. Thus, each new pixel is a weighted average of a small number of pixels in the original image.

The [Image::ResampleWeights()](#Image::ResampleWeights) method utility determines which original pixels contribute to each new pixel and what the values are of the contribution weights for each new pixel. It returns the values in an array of [ResampleWeight](#ResampleWeight) structures for all the pixels in a 1D row or column of the image. Because this information is the same for all rows of the image when resampling in and all columns when resampling in, it is more efficient to compute it once for each of the two passes and then reuse it many times for each one.

For the reconstruction filter used here, no more than four of the original pixels will contribute to each new pixel after resizing, so [ResampleWeight](#ResampleWeight) only needs to hold four weights. Because the four pixels are contiguous, we only store the offset to the first one.

<<ResampleWeight Definition>>=

struct ResampleWeight { int firstPixel; Float weight\[4\]; };

<<Image Method Definitions>>=

std::vector<ResampleWeight> [Image](#Image)::ResampleWeights(int oldRes, int newRes) { std::vector<ResampleWeight> wt(newRes); Float filterRadius = 2, tau = 2; for (int i = 0; i < newRes; ++i) { << [Compute image resampling weights for i th pixel](#fragment-Computeimageresamplingweightsformonoithpixel-0) >>

Float center = (i +.5f) \* oldRes / newRes; wt\[i\].firstPixel = pstd::floor((center - filterRadius) + 0.5f); for (int j = 0; j < 4; ++j) { Float pos = wt\[i\].firstPixel + j +.5f; wt\[i\].weight\[j\] = [WindowedSinc](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Image_Reconstruction.html#WindowedSinc) (pos - center, filterRadius, tau); }

<< [Normalize filter weights for pixel resampling](#fragment-Normalizefilterweightsforpixelresampling-0) >>

Float invSumWts = 1 / (wt\[i\].weight\[0\] + wt\[i\].weight\[1\] + wt\[i\].weight\[2\] + wt\[i\].weight\[3\]); for (int j = 0; j < 4; ++j) wt\[i\].weight\[j\] \*= invSumWts;

} return wt; }

Here is another instance where it is important to distinguish between discrete and continuous pixel coordinates. For each pixel in the resampled image, this function starts by computing its continuous coordinates in terms of the source image’s pixel coordinates. This value is stored in center, because it is the center of the reconstruction filter for the new pixel. Next, it is necessary to find the offset to the first pixel that contributes to the new pixel. This is a slightly tricky calculation—after subtracting the filter width to find the start of the filter’s nonzero range, it is necessary to add an extra offset to the continuous coordinate before taking the floor to find the discrete coordinate. Figure [B.7](#fig:resamp-half-offset) illustrates why this offset is needed.

[![](https://pbr-book.org/4ed/Utilities/phabbf07.svg)](https://pbr-book.org/4ed/Utilities/phabbf07.svg)

Figure B.7: The computation to find the first pixel inside a reconstruction filter’s support is slightly tricky. Consider a filter centered around continuous coordinate 2.75 with radius 2, as shown here. The filter’s support covers the range, although pixel zero is outside the filter’s support: adding to the lower end before taking the floor to find the discrete pixel gives the correct starting pixel, number one.

Starting from that first contributing pixel, this function loops over four pixels, computing each one’s offset to the center of the filter kernel and the corresponding filter weight.

<<Compute image resampling weights for i th pixel>>=

Float center = (i +.5f) \* oldRes / newRes; wt\[i\].firstPixel = pstd::floor((center - filterRadius) + 0.5f); for (int j = 0; j < 4; ++j) { Float pos = wt\[i\].firstPixel + j +.5f; wt\[i\].weight\[j\] = [WindowedSinc](https://pbr-book.org/4ed/Sampling_and_Reconstruction/Image_Reconstruction.html#WindowedSinc) (pos - center, filterRadius, tau); }

The four filter weights generally do not sum to one. Therefore, to ensure that the resampled image will not be any brighter or darker than the original image, the weights are normalized here.

<<Normalize filter weights for pixel resampling>>=

Float invSumWts = 1 / (wt\[i\].weight\[0\] + wt\[i\].weight\[1\] + wt\[i\].weight\[2\] + wt\[i\].weight\[3\]); for (int j = 0; j < 4; ++j) wt\[i\].weight\[j\] \*= invSumWts;

Given ResampleWeights(), we can continue to FloatResizeUp(), which resizes an image to a higher resolution and returns the result, with pixels stored as Float s in memory, regardless of the input image format.

[Image](#Image) [Image](#Image)::FloatResizeUp([Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) newRes, WrapMode2D wrapMode) const { [Image](#Image) resampledImage(PixelFormat::Float, newRes, [channelNames](#Image::channelNames)); << [Compute and resampling weights for image resizing](#fragment-Computexandyresamplingweightsforimageresizing-0) >>

std::vector<ResampleWeight> xWeights, yWeights; xWeights = [ResampleWeights](#Image::ResampleWeights) ([resolution](#Image::resolution) \[0\], newRes\[0\]); yWeights = [ResampleWeights](#Image::ResampleWeights) ([resolution](#Image::resolution) \[1\], newRes\[1\]);

<< [Resize image in parallel, working by tiles](#fragment-Resizeimageinparallelworkingbytiles-0) >>

[ParallelFor2D](https://pbr-book.org/4ed/Utilities/Parallelism.html#ParallelFor2D) ([Bounds2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2i) ({0, 0}, newRes), \[&\]([Bounds2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2i) outExtent) { << [Determine extent in source image and copy pixel values to inBuf](#fragment-DetermineextentinsourceimageandcopypixelvaluestomonoinBuf-0) >>

[Bounds2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2i) inExtent([Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) (xWeights\[outExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).x\].firstPixel, yWeights\[outExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).y\].firstPixel), [Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) (xWeights\[outExtent.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).x - 1\].firstPixel + 4, yWeights\[outExtent.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).y - 1\].firstPixel + 4)); std::vector<float> inBuf([NChannels](#Image::NChannels) () \* inExtent.[Area](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::Area) ()); [CopyRectOut](#Image::CopyRectOut) (inExtent, pstd::span<float>(inBuf), wrapMode);

<< [Resize image in the dimension](#fragment-Resizeimageinthexdimension-0) >>

<< [Compute image extents and allocate xBuf](#fragment-ComputeimageextentsandallocatemonoxBuf-0) >>

int nxOut = outExtent.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).x - outExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).x; int nyOut = outExtent.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).y - outExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).y; int nxIn = inExtent.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).x - inExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).x; int nyIn = inExtent.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).y - inExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).y; std::vector<float> xBuf([NChannels](#Image::NChannels) () \* nyIn \* nxOut);

int xBufOffset = 0; for (int yOut = inExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).y; yOut < inExtent.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).y; ++yOut) { for (int xOut = outExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).x; xOut < outExtent.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).x; ++xOut) { << [Resample image pixel (xOut, yOut)](#fragment-ResampleimagepixelmonoxOutyOut-0) >>

const ResampleWeight &rsw = xWeights\[xOut\]; << [Compute inOffset into inBuf for (xOut, yOut)](#fragment-ComputemonoinOffsetintomonoinBufformonoxOutyOut-0) >>

int xIn = rsw.firstPixel - inExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).x; int yIn = yOut - inExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).y; int inOffset = [NChannels](#Image::NChannels) () \* (xIn + yIn \* nxIn);

for (int c = 0; c < [NChannels](#Image::NChannels) (); ++c, ++xBufOffset, ++inOffset) xBuf\[xBufOffset\] = rsw.weight\[0\] \* inBuf\[inOffset\] + rsw.weight\[1\] \* inBuf\[inOffset + [NChannels](#Image::NChannels) ()\] + rsw.weight\[2\] \* inBuf\[inOffset + 2 \* [NChannels](#Image::NChannels) ()\] + rsw.weight\[3\] \* inBuf\[inOffset + 3 \* [NChannels](#Image::NChannels) ()\];

} }

<<Resize image in the dimension>>

std::vector<float> outBuf(NChannels() \* nxOut \* nyOut); for (int x = 0; x < nxOut; ++x) { for (int y = 0; y < nyOut; ++y) { int yOut = y + outExtent\[0\]\[1\]; DCHECK(yOut >= 0 && yOut < yWeights.size()); const ResampleWeight &rsw = yWeights\[yOut\]; DCHECK\_GE(rsw.firstPixel - inExtent\[0\]\[1\], 0); int xBufOffset = NChannels() \* (x + nxOut \* (rsw.firstPixel - inExtent\[0\]\[1\])); DCHECK\_GE(xBufOffset, 0); int step = NChannels() \* nxOut; DCHECK\_LT(xBufOffset + 3 \* step, xBuf.size()); int outOffset = NChannels() \* (x + y \* nxOut); for (int c = 0; c < NChannels(); ++c, ++outOffset, ++xBufOffset) outBuf\[outOffset\] = std::max<Float>(0, (rsw.weight\[0\] \* xBuf\[xBufOffset\] + rsw.weight\[1\] \* xBuf\[xBufOffset + step\] + rsw.weight\[2\] \* xBuf\[xBufOffset + 2 \* step\] + rsw.weight\[3\] \* xBuf\[xBufOffset + 3 \* step\])); } }

<< [Copy resampled image pixels out into resampledImage](#fragment-CopyresampledimagepixelsoutintomonoresampledImage-0) >>

resampledImage.[CopyRectIn](#Image::CopyRectIn) (outExtent, outBuf);

});

return resampledImage; }

<<Compute and resampling weights for image resizing>>=

std::vector<ResampleWeight> xWeights, yWeights; xWeights = [ResampleWeights](#Image::ResampleWeights) ([resolution](#Image::resolution) \[0\], newRes\[0\]); yWeights = [ResampleWeights](#Image::ResampleWeights) ([resolution](#Image::resolution) \[1\], newRes\[1\]);

Given filter weights, the image is resized in parallel, where threads work on tiles of the output image. Although this parallelism scheme leads to some redundant work among threads from the need to compute extra pixel values at the boundaries of tiles, it has the advantage that the second filtering operation in has a more compact memory access pattern, which gives a performance benefit from better cache coherence.

<<Resize image in parallel, working by tiles>>=

[ParallelFor2D](https://pbr-book.org/4ed/Utilities/Parallelism.html#ParallelFor2D) ([Bounds2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2i) ({0, 0}, newRes), \[&\]([Bounds2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2i) outExtent) { << [Determine extent in source image and copy pixel values to inBuf](#fragment-DetermineextentinsourceimageandcopypixelvaluestomonoinBuf-0) >>

[Bounds2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2i) inExtent([Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) (xWeights\[outExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).x\].firstPixel, yWeights\[outExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).y\].firstPixel), [Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) (xWeights\[outExtent.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).x - 1\].firstPixel + 4, yWeights\[outExtent.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).y - 1\].firstPixel + 4)); std::vector<float> inBuf([NChannels](#Image::NChannels) () \* inExtent.[Area](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::Area) ()); [CopyRectOut](#Image::CopyRectOut) (inExtent, pstd::span<float>(inBuf), wrapMode);

<< [Resize image in the dimension](#fragment-Resizeimageinthexdimension-0) >>

<< [Compute image extents and allocate xBuf](#fragment-ComputeimageextentsandallocatemonoxBuf-0) >>

int nxOut = outExtent.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).x - outExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).x; int nyOut = outExtent.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).y - outExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).y; int nxIn = inExtent.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).x - inExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).x; int nyIn = inExtent.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).y - inExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).y; std::vector<float> xBuf([NChannels](#Image::NChannels) () \* nyIn \* nxOut);

int xBufOffset = 0; for (int yOut = inExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).y; yOut < inExtent.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).y; ++yOut) { for (int xOut = outExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).x; xOut < outExtent.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).x; ++xOut) { << [Resample image pixel (xOut, yOut)](#fragment-ResampleimagepixelmonoxOutyOut-0) >>

const ResampleWeight &rsw = xWeights\[xOut\]; << [Compute inOffset into inBuf for (xOut, yOut)](#fragment-ComputemonoinOffsetintomonoinBufformonoxOutyOut-0) >>

int xIn = rsw.firstPixel - inExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).x; int yIn = yOut - inExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).y; int inOffset = [NChannels](#Image::NChannels) () \* (xIn + yIn \* nxIn);

for (int c = 0; c < [NChannels](#Image::NChannels) (); ++c, ++xBufOffset, ++inOffset) xBuf\[xBufOffset\] = rsw.weight\[0\] \* inBuf\[inOffset\] + rsw.weight\[1\] \* inBuf\[inOffset + [NChannels](#Image::NChannels) ()\] + rsw.weight\[2\] \* inBuf\[inOffset + 2 \* [NChannels](#Image::NChannels) ()\] + rsw.weight\[3\] \* inBuf\[inOffset + 3 \* [NChannels](#Image::NChannels) ()\];

} }

<<Resize image in the dimension>>

std::vector<float> outBuf(NChannels() \* nxOut \* nyOut); for (int x = 0; x < nxOut; ++x) { for (int y = 0; y < nyOut; ++y) { int yOut = y + outExtent\[0\]\[1\]; DCHECK(yOut >= 0 && yOut < yWeights.size()); const ResampleWeight &rsw = yWeights\[yOut\]; DCHECK\_GE(rsw.firstPixel - inExtent\[0\]\[1\], 0); int xBufOffset = NChannels() \* (x + nxOut \* (rsw.firstPixel - inExtent\[0\]\[1\])); DCHECK\_GE(xBufOffset, 0); int step = NChannels() \* nxOut; DCHECK\_LT(xBufOffset + 3 \* step, xBuf.size()); int outOffset = NChannels() \* (x + y \* nxOut); for (int c = 0; c < NChannels(); ++c, ++outOffset, ++xBufOffset) outBuf\[outOffset\] = std::max<Float>(0, (rsw.weight\[0\] \* xBuf\[xBufOffset\] + rsw.weight\[1\] \* xBuf\[xBufOffset + step\] + rsw.weight\[2\] \* xBuf\[xBufOffset + 2 \* step\] + rsw.weight\[3\] \* xBuf\[xBufOffset + 3 \* step\])); } }

<< [Copy resampled image pixels out into resampledImage](#fragment-CopyresampledimagepixelsoutintomonoresampledImage-0) >>

resampledImage.[CopyRectIn](#Image::CopyRectIn) (outExtent, outBuf);

});

The first step copies all the pixel values that will be needed from the source image to compute the pixels in outExtent into a local buffer, inBuf. There are two reasons for doing this (versus accessing pixel values as needed from the input image): first, CopyRectOut() is generally more efficient than accessing the pixel channel values individually since not only are boundary conditions handled just once, but any necessary format conversion to Float is also done once for each pixel channel and in bulk. Second, the pixel channel values that will be accessed for subsequent filtering computations end up being contiguous in memory, which also improves cache coherence.

<<Determine extent in source image and copy pixel values to inBuf >>=

[Bounds2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2i) inExtent([Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) (xWeights\[outExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).x\].firstPixel, yWeights\[outExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).y\].firstPixel), [Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) (xWeights\[outExtent.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).x - 1\].firstPixel + 4, yWeights\[outExtent.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).y - 1\].firstPixel + 4)); std::vector<float> inBuf([NChannels](#Image::NChannels) () \* inExtent.[Area](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::Area) ()); [CopyRectOut](#Image::CopyRectOut) (inExtent, pstd::span<float>(inBuf), wrapMode);

After allocating a temporary buffer for the -resampled image, the following loops iterate over all its pixels to compute their resampled channel values.

<<Resize image in the dimension>>=

<< [Compute image extents and allocate xBuf](#fragment-ComputeimageextentsandallocatemonoxBuf-0) >>

int nxOut = outExtent.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).x - outExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).x; int nyOut = outExtent.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).y - outExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).y; int nxIn = inExtent.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).x - inExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).x; int nyIn = inExtent.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).y - inExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).y; std::vector<float> xBuf([NChannels](#Image::NChannels) () \* nyIn \* nxOut);

int xBufOffset = 0; for (int yOut = inExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).y; yOut < inExtent.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).y; ++yOut) { for (int xOut = outExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).x; xOut < outExtent.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).x; ++xOut) { << [Resample image pixel (xOut, yOut)](#fragment-ResampleimagepixelmonoxOutyOut-0) >>

const ResampleWeight &rsw = xWeights\[xOut\]; << [Compute inOffset into inBuf for (xOut, yOut)](#fragment-ComputemonoinOffsetintomonoinBufformonoxOutyOut-0) >>

int xIn = rsw.firstPixel - inExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).x; int yIn = yOut - inExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).y; int inOffset = [NChannels](#Image::NChannels) () \* (xIn + yIn \* nxIn);

for (int c = 0; c < [NChannels](#Image::NChannels) (); ++c, ++xBufOffset, ++inOffset) xBuf\[xBufOffset\] = rsw.weight\[0\] \* inBuf\[inOffset\] + rsw.weight\[1\] \* inBuf\[inOffset + [NChannels](#Image::NChannels) ()\] + rsw.weight\[2\] \* inBuf\[inOffset + 2 \* [NChannels](#Image::NChannels) ()\] + rsw.weight\[3\] \* inBuf\[inOffset + 3 \* [NChannels](#Image::NChannels) ()\];

} }

The result of the resampling step will be stored in xBuf. Note that it is necessary to perform the resampling across all the scanlines in inExtent ’s range, as the -resampled instances of them will be needed for the resampling step.

<<Compute image extents and allocate xBuf >>=

int nxOut = outExtent.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).x - outExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).x; int nyOut = outExtent.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).y - outExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).y; int nxIn = inExtent.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).x - inExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).x; int nyIn = inExtent.[pMax](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMax).y - inExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).y; std::vector<float> xBuf([NChannels](#Image::NChannels) () \* nyIn \* nxOut);

Once all the values are lined up, the actual resampling operation is straightforward—effectively just the inner product of the normalized filter weights and pixel channel values.

<<Resample image pixel (xOut, yOut) >>=

const ResampleWeight &rsw = xWeights\[xOut\]; << [Compute inOffset into inBuf for (xOut, yOut)](#fragment-ComputemonoinOffsetintomonoinBufformonoxOutyOut-0) >>

int xIn = rsw.firstPixel - inExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).x; int yIn = yOut - inExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).y; int inOffset = [NChannels](#Image::NChannels) () \* (xIn + yIn \* nxIn);

for (int c = 0; c < [NChannels](#Image::NChannels) (); ++c, ++xBufOffset, ++inOffset) xBuf\[xBufOffset\] = rsw.weight\[0\] \* inBuf\[inOffset\] + rsw.weight\[1\] \* inBuf\[inOffset + [NChannels](#Image::NChannels) ()\] + rsw.weight\[2\] \* inBuf\[inOffset + 2 \* [NChannels](#Image::NChannels) ()\] + rsw.weight\[3\] \* inBuf\[inOffset + 3 \* [NChannels](#Image::NChannels) ()\];

The (xOut, yOut) pixel coordinate is with respect to the overall final resampled image. However, only the necessary input pixels for the tile have been copied to inBuf. Therefore, some reindexing is necessary to compute the offset into inBuf that corresponds to the first pixel that will be accessed to compute (xOut, yOut) ’s -resized value.

<<Compute inOffset into inBuf for (xOut, yOut) >>=

int xIn = rsw.firstPixel - inExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).x; int yIn = yOut - inExtent.[pMin](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2::pMin).y; int inOffset = [NChannels](#Image::NChannels) () \* (xIn + yIn \* nxIn);

The fragment <<Resize image in the dimension>> follows a similar approach but filters along, going from xBuf into outBuf. It is therefore not included here.

Given resampled pixels for outExtent, they can be copied in bulk to the output image via CopyRectIn().

<<Copy resampled image pixels out into resampledImage >>=

resampledImage.[CopyRectIn](#Image::CopyRectIn) (outExtent, outBuf);

### B.5.5 Image Pyramids

The GeneratePyramid() method generates an image pyramid, which stores a source image, first resized if necessary to have power-of-two resolution in each dimension, at its base. Higher levels of the pyramid are successively found by downsampling the next lower level by a factor of two in each dimension. Image pyramids are widely used for accelerating image filtering operations and are a cornerstone of MIP mapping, which is implemented in pbrt ’s [MIPMap](https://pbr-book.org/4ed/Textures_and_Materials/Image_Texture.html#MIPMap) class, defined in Section [10.4.3](https://pbr-book.org/4ed/Textures_and_Materials/Image_Texture.html#sec:mipmap).

<<Image Method Definitions>>+=

pstd::vector< [Image](#Image) > [Image](#Image)::GeneratePyramid([Image](#Image) image, WrapMode2D wrapMode, [Allocator](https://pbr-book.org/4ed/Introduction/Using_and_Understanding_the_Code.html#Allocator) alloc) { PixelFormat origFormat = image.[format](#Image::format); int nChannels = image.[NChannels](#Image::NChannels) (); [ColorEncoding](#ColorEncoding) origEncoding = image.[encoding](#Image::encoding); << [Prepare image for building pyramid](#fragment-Preparemonoimageforbuildingpyramid-0) >> << [Initialize levels of pyramid from image](#fragment-Initializelevelsofpyramidfrommonoimage-0) >>

int nLevels = 1 + [Log2Int](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Log2Int) (std::max(image.[resolution](#Image::resolution) \[0\], image.[resolution](#Image::resolution) \[1\])); pstd::vector< [Image](#Image) > pyramid(alloc); for (int i = 0; i < nLevels - 1; ++i) { << [Initialize st level from th level and copy th into pyramid](#fragment-Initializei1stlevelfromithlevelandcopyithintopyramid-0) >>

pyramid.push\_back([Image](#Image) (origFormat, image.[resolution](#Image::resolution), image.[channelNames](#Image::channelNames), origEncoding, alloc)); << [Initialize nextImage for st level](#fragment-InitializemononextImagefori1stlevel-0) >>

[Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) nextResolution(std::max(1, image.[resolution](#Image::resolution) \[0\] / 2), std::max(1, image.[resolution](#Image::resolution) \[1\] / 2)); [Image](#Image) nextImage(image.[format](#Image::format), nextResolution, image.[channelNames](#Image::channelNames), origEncoding);

<< [Compute offsets from pixels to the 4 pixels used for downsampling](#fragment-Computeoffsetsfrompixelstothe4pixelsusedfordownsampling-0) >>

int srcDeltas\[4\] = {0, nChannels, nChannels \* image.[resolution](#Image::resolution) \[0\], nChannels \* (image.[resolution](#Image::resolution) \[0\] + 1)}; if (image.[resolution](#Image::resolution) \[0\] == 1) { srcDeltas\[1\] = 0; srcDeltas\[3\] -= nChannels; } if (image.[resolution](#Image::resolution) \[1\] == 1) { srcDeltas\[2\] = 0; srcDeltas\[3\] -= nChannels \* image.[resolution](#Image::resolution) \[0\]; }

<< [Downsample image to create next level and update pyramid](#fragment-Downsamplemonoimagetocreatenextlevelandupdatemonopyramid-0) >>

[ParallelFor](https://pbr-book.org/4ed/Utilities/Parallelism.html#ParallelFor) (0, nextResolution\[1\], \[&\](int64\_t y) { << [Loop over pixels in scanline and downsample for the next pyramid level](#fragment-Loopoverpixelsinscanlineyanddownsampleforthenextpyramidlevel-0) >>

int srcOffset = image.[PixelOffset](#Image::PixelOffset) ([Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) (0, 2 \* int(y))); int nextOffset = nextImage.[PixelOffset](#Image::PixelOffset) ([Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) (0, int(y))); for (int x = 0; x < nextResolution\[0\]; ++x, srcOffset += nChannels) for (int c = 0; c < nChannels; ++c, ++srcOffset, ++nextOffset) nextImage.[p32](#Image::p32) \[nextOffset\] = (image.[p32](#Image::p32) \[srcOffset\] + image.[p32](#Image::p32) \[srcOffset + srcDeltas\[1\]\] + image.[p32](#Image::p32) \[srcOffset + srcDeltas\[2\]\] + image.[p32](#Image::p32) \[srcOffset + srcDeltas\[3\]\]) / 4;

<< [Copy two scanlines from image out to its pyramid level](#fragment-Copytwoscanlinesfrommonoimageouttoitspyramidlevel-0) >>

int yStart = 2 \* y; int yEnd = std::min(2 \* int(y) + 2, image.[resolution](#Image::resolution) \[1\]); int offset = image.[PixelOffset](#Image::PixelOffset) ({0, yStart}); size\_t count = (yEnd - yStart) \* nChannels \* image.[resolution](#Image::resolution) \[0\]; pyramid\[i\].[CopyRectIn](#Image::CopyRectIn) ([Bounds2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2i) ({0, yStart}, {image.[resolution](#Image::resolution) \[0\], yEnd}), pstd::span<const float>(image.[p32](#Image::p32).data() + offset, count));

}); image = std::move(nextImage);

}

<< [Initialize top level of pyramid and return it](#fragment-Initializetoplevelofpyramidandreturnit-0) >>

pyramid.push\_back([Image](#Image) (origFormat, {1, 1}, image.[channelNames](#Image::channelNames), origEncoding, alloc)); pyramid\[nLevels - 1\].[CopyRectIn](#Image::CopyRectIn) ([Bounds2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2i) ({0, 0}, {1, 1}), pstd::span<const float>(image.[p32](#Image::p32).data(), nChannels)); return pyramid;

}

Implementation of an image pyramid is easier if the resolution of the original image is an exact power of two in each direction; this ensures that there is a direct relationship between the level of the pyramid and the number of texels at that level. If the user has provided an image where the resolution in one or both of the dimensions is not a power of two, then the GeneratePyramid() method calls FloatResizeUp() to resize the image up to the next power-of-two resolution greater than the original resolution before constructing the pyramid. (Exercise B.1 at the end of the chapter describes an approach for building image pyramids with non-power-of-two resolutions.)

Otherwise, if the provided image does not use 32-bit float s for its in-memory format, it is converted to that representation. This helps avoid errors in the image pyramid due to insufficient precision being used for the inputs to the filtering computations. (In the end, however, the returned pyramid will have images in the format of the original image so that memory use is not unnecessarily increased.)

These two operations motivate taking the [Image](#Image) as a parameter to a static method, as GeneratePyramid() is, rather than being a non- static member function. Thus, a new image can easily be reassigned to image as necessary.

<<Prepare image for building pyramid>>=

Once we have a floating-point image with resolutions that are powers of two, the levels of the MIP map can be initialized, starting from the bottom (finest) level. Each higher level is found by filtering the texels from the previous level.

<<Initialize levels of pyramid from image >>=

int nLevels = 1 + [Log2Int](https://pbr-book.org/4ed/Utilities/Mathematical_Infrastructure.html#Log2Int) (std::max(image.[resolution](#Image::resolution) \[0\], image.[resolution](#Image::resolution) \[1\])); pstd::vector< [Image](#Image) > pyramid(alloc); for (int i = 0; i < nLevels - 1; ++i) { << [Initialize st level from th level and copy th into pyramid](#fragment-Initializei1stlevelfromithlevelandcopyithintopyramid-0) >>

pyramid.push\_back([Image](#Image) (origFormat, image.[resolution](#Image::resolution), image.[channelNames](#Image::channelNames), origEncoding, alloc)); << [Initialize nextImage for st level](#fragment-InitializemononextImagefori1stlevel-0) >>

[Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) nextResolution(std::max(1, image.[resolution](#Image::resolution) \[0\] / 2), std::max(1, image.[resolution](#Image::resolution) \[1\] / 2)); [Image](#Image) nextImage(image.[format](#Image::format), nextResolution, image.[channelNames](#Image::channelNames), origEncoding);

<< [Compute offsets from pixels to the 4 pixels used for downsampling](#fragment-Computeoffsetsfrompixelstothe4pixelsusedfordownsampling-0) >>

int srcDeltas\[4\] = {0, nChannels, nChannels \* image.[resolution](#Image::resolution) \[0\], nChannels \* (image.[resolution](#Image::resolution) \[0\] + 1)}; if (image.[resolution](#Image::resolution) \[0\] == 1) { srcDeltas\[1\] = 0; srcDeltas\[3\] -= nChannels; } if (image.[resolution](#Image::resolution) \[1\] == 1) { srcDeltas\[2\] = 0; srcDeltas\[3\] -= nChannels \* image.[resolution](#Image::resolution) \[0\]; }

<< [Downsample image to create next level and update pyramid](#fragment-Downsamplemonoimagetocreatenextlevelandupdatemonopyramid-0) >>

[ParallelFor](https://pbr-book.org/4ed/Utilities/Parallelism.html#ParallelFor) (0, nextResolution\[1\], \[&\](int64\_t y) { << [Loop over pixels in scanline and downsample for the next pyramid level](#fragment-Loopoverpixelsinscanlineyanddownsampleforthenextpyramidlevel-0) >>

int srcOffset = image.[PixelOffset](#Image::PixelOffset) ([Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) (0, 2 \* int(y))); int nextOffset = nextImage.[PixelOffset](#Image::PixelOffset) ([Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) (0, int(y))); for (int x = 0; x < nextResolution\[0\]; ++x, srcOffset += nChannels) for (int c = 0; c < nChannels; ++c, ++srcOffset, ++nextOffset) nextImage.[p32](#Image::p32) \[nextOffset\] = (image.[p32](#Image::p32) \[srcOffset\] + image.[p32](#Image::p32) \[srcOffset + srcDeltas\[1\]\] + image.[p32](#Image::p32) \[srcOffset + srcDeltas\[2\]\] + image.[p32](#Image::p32) \[srcOffset + srcDeltas\[3\]\]) / 4;

<< [Copy two scanlines from image out to its pyramid level](#fragment-Copytwoscanlinesfrommonoimageouttoitspyramidlevel-0) >>

int yStart = 2 \* y; int yEnd = std::min(2 \* int(y) + 2, image.[resolution](#Image::resolution) \[1\]); int offset = image.[PixelOffset](#Image::PixelOffset) ({0, yStart}); size\_t count = (yEnd - yStart) \* nChannels \* image.[resolution](#Image::resolution) \[0\]; pyramid\[i\].[CopyRectIn](#Image::CopyRectIn) ([Bounds2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2i) ({0, yStart}, {image.[resolution](#Image::resolution) \[0\], yEnd}), pstd::span<const float>(image.[p32](#Image::p32).data() + offset, count));

}); image = std::move(nextImage);

}

Each time through this loop, image starts out as the already-filtered image for the th level that will be downsampled to generate the image for the st level. A new entry is added to the image pyramid for image, though using the original pixel format.

<<Initialize st level from th level and copy th into pyramid>>=

pyramid.push\_back([Image](#Image) (origFormat, image.[resolution](#Image::resolution), image.[channelNames](#Image::channelNames), origEncoding, alloc)); << [Initialize nextImage for st level](#fragment-InitializemononextImagefori1stlevel-0) >>

[Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) nextResolution(std::max(1, image.[resolution](#Image::resolution) \[0\] / 2), std::max(1, image.[resolution](#Image::resolution) \[1\] / 2)); [Image](#Image) nextImage(image.[format](#Image::format), nextResolution, image.[channelNames](#Image::channelNames), origEncoding);

<< [Compute offsets from pixels to the 4 pixels used for downsampling](#fragment-Computeoffsetsfrompixelstothe4pixelsusedfordownsampling-0) >>

int srcDeltas\[4\] = {0, nChannels, nChannels \* image.[resolution](#Image::resolution) \[0\], nChannels \* (image.[resolution](#Image::resolution) \[0\] + 1)}; if (image.[resolution](#Image::resolution) \[0\] == 1) { srcDeltas\[1\] = 0; srcDeltas\[3\] -= nChannels; } if (image.[resolution](#Image::resolution) \[1\] == 1) { srcDeltas\[2\] = 0; srcDeltas\[3\] -= nChannels \* image.[resolution](#Image::resolution) \[0\]; }

<< [Downsample image to create next level and update pyramid](#fragment-Downsamplemonoimagetocreatenextlevelandupdatemonopyramid-0) >>

[ParallelFor](https://pbr-book.org/4ed/Utilities/Parallelism.html#ParallelFor) (0, nextResolution\[1\], \[&\](int64\_t y) { << [Loop over pixels in scanline and downsample for the next pyramid level](#fragment-Loopoverpixelsinscanlineyanddownsampleforthenextpyramidlevel-0) >>

int srcOffset = image.[PixelOffset](#Image::PixelOffset) ([Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) (0, 2 \* int(y))); int nextOffset = nextImage.[PixelOffset](#Image::PixelOffset) ([Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) (0, int(y))); for (int x = 0; x < nextResolution\[0\]; ++x, srcOffset += nChannels) for (int c = 0; c < nChannels; ++c, ++srcOffset, ++nextOffset) nextImage.[p32](#Image::p32) \[nextOffset\] = (image.[p32](#Image::p32) \[srcOffset\] + image.[p32](#Image::p32) \[srcOffset + srcDeltas\[1\]\] + image.[p32](#Image::p32) \[srcOffset + srcDeltas\[2\]\] + image.[p32](#Image::p32) \[srcOffset + srcDeltas\[3\]\]) / 4;

<< [Copy two scanlines from image out to its pyramid level](#fragment-Copytwoscanlinesfrommonoimageouttoitspyramidlevel-0) >>

int yStart = 2 \* y; int yEnd = std::min(2 \* int(y) + 2, image.[resolution](#Image::resolution) \[1\]); int offset = image.[PixelOffset](#Image::PixelOffset) ({0, yStart}); size\_t count = (yEnd - yStart) \* nChannels \* image.[resolution](#Image::resolution) \[0\]; pyramid\[i\].[CopyRectIn](#Image::CopyRectIn) ([Bounds2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2i) ({0, yStart}, {image.[resolution](#Image::resolution) \[0\], yEnd}), pstd::span<const float>(image.[p32](#Image::p32).data() + offset, count));

}); image = std::move(nextImage);

For non-square images, the resolution in one direction must be clamped to 1 for the upper levels of the image pyramid, where there is still downsampling to do in the larger of the two resolutions. This is handled by the following std::max() calls:

<<Initialize nextImage for st level>>=

[Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) nextResolution(std::max(1, image.[resolution](#Image::resolution) \[0\] / 2), std::max(1, image.[resolution](#Image::resolution) \[1\] / 2)); [Image](#Image) nextImage(image.[format](#Image::format), nextResolution, image.[channelNames](#Image::channelNames), origEncoding);

GeneratePyramid() uses a simple box filter to average four texels from the previous level to find the value at the current texel. Using the Lanczos filter here would give a slightly better result for this computation, although this modification is left for Exercise B.2 at the end of the chapter.

With the box filter, each pixel in nextImage is given by the average of the pixels,,, and. Here we compute the corresponding offsets from a pixel in the source image to those four pixels; doing this here saves some math in pixel indexing when downsampling. These offsets are based on the scanline-based layout of [Image](#Image) data in memory; referring to the implementation of [Image::PixelOffset()](#Image::PixelOffset) may make their operation more clear.

Here is also a good chance to handle images with single pixel resolution in one dimension; in that case the offsets are set so that valid pixels are used twice, and the downsampling loop can be written to always assume four values.

<<Compute offsets from pixels to the 4 pixels used for downsampling>>=

int srcDeltas\[4\] = {0, nChannels, nChannels \* image.[resolution](#Image::resolution) \[0\], nChannels \* (image.[resolution](#Image::resolution) \[0\] + 1)}; if (image.[resolution](#Image::resolution) \[0\] == 1) { srcDeltas\[1\] = 0; srcDeltas\[3\] -= nChannels; } if (image.[resolution](#Image::resolution) \[1\] == 1) { srcDeltas\[2\] = 0; srcDeltas\[3\] -= nChannels \* image.[resolution](#Image::resolution) \[0\]; }

The work for the current level is performed in parallel since each output pixel’s value is independent of the others. For scenes with many textures, MIP map generation may be a meaningful amount of pbrt ’s startup time, so it is worthwhile to try to optimize this work done by this method so that rendering can begin more quickly. When the work for each level is finished, the image for the next level is assigned to image so that the loop can proceed once again.

<<Downsample image to create next level and update pyramid >>=

[ParallelFor](https://pbr-book.org/4ed/Utilities/Parallelism.html#ParallelFor) (0, nextResolution\[1\], \[&\](int64\_t y) { << [Loop over pixels in scanline and downsample for the next pyramid level](#fragment-Loopoverpixelsinscanlineyanddownsampleforthenextpyramidlevel-0) >>

int srcOffset = image.[PixelOffset](#Image::PixelOffset) ([Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) (0, 2 \* int(y))); int nextOffset = nextImage.[PixelOffset](#Image::PixelOffset) ([Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) (0, int(y))); for (int x = 0; x < nextResolution\[0\]; ++x, srcOffset += nChannels) for (int c = 0; c < nChannels; ++c, ++srcOffset, ++nextOffset) nextImage.[p32](#Image::p32) \[nextOffset\] = (image.[p32](#Image::p32) \[srcOffset\] + image.[p32](#Image::p32) \[srcOffset + srcDeltas\[1\]\] + image.[p32](#Image::p32) \[srcOffset + srcDeltas\[2\]\] + image.[p32](#Image::p32) \[srcOffset + srcDeltas\[3\]\]) / 4;

<< [Copy two scanlines from image out to its pyramid level](#fragment-Copytwoscanlinesfrommonoimageouttoitspyramidlevel-0) >>

int yStart = 2 \* y; int yEnd = std::min(2 \* int(y) + 2, image.[resolution](#Image::resolution) \[1\]); int offset = image.[PixelOffset](#Image::PixelOffset) ({0, yStart}); size\_t count = (yEnd - yStart) \* nChannels \* image.[resolution](#Image::resolution) \[0\]; pyramid\[i\].[CopyRectIn](#Image::CopyRectIn) ([Bounds2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2i) ({0, yStart}, {image.[resolution](#Image::resolution) \[0\], yEnd}), pstd::span<const float>(image.[p32](#Image::p32).data() + offset, count));

}); image = std::move(nextImage);

The following fragment computes a scanline’s worth of downsampled pixel values in nextImage. It makes extensive use of the fact that the channels for each pixel are laid out consecutively in memory and that pixels are stored in scanline order in memory. Thus, it can compute offsets into the pixel arrays for the scanline starting at and efficiently incrementally update them for each image channel and each pixel. Note also that the offsets to the neighboring pixels from srcDeltas are used to efficiently find the necessary pixel values from image.

<<Loop over pixels in scanline and downsample for the next pyramid level>>=

int srcOffset = image.[PixelOffset](#Image::PixelOffset) ([Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) (0, 2 \* int(y))); int nextOffset = nextImage.[PixelOffset](#Image::PixelOffset) ([Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) (0, int(y))); for (int x = 0; x < nextResolution\[0\]; ++x, srcOffset += nChannels) for (int c = 0; c < nChannels; ++c, ++srcOffset, ++nextOffset) nextImage.[p32](#Image::p32) \[nextOffset\] = (image.[p32](#Image::p32) \[srcOffset\] + image.[p32](#Image::p32) \[srcOffset + srcDeltas\[1\]\] + image.[p32](#Image::p32) \[srcOffset + srcDeltas\[2\]\] + image.[p32](#Image::p32) \[srcOffset + srcDeltas\[3\]\]) / 4;

We will take advantage of the fact that processing is happening in parallel here to also copy pixel values from image into their place in the pyramid. Doing so here has the added benefit that the pixel values should already be in the cache from their use as inputs to the downsampling computation.

Because the ParallelFor() loop is over scanlines in the lower-resolution image, two scanlines from image are copied here except in the edge case of a single-scanline-high image from a non-square-input image. The [Image](#Image) CopyRectIn() method copies the pixels inside the provided bounds, taking care of converting them to the format of the destination image pixels if necessary.

<<Copy two scanlines from image out to its pyramid level>>=

int yStart = 2 \* y; int yEnd = std::min(2 \* int(y) + 2, image.[resolution](#Image::resolution) \[1\]); int offset = image.[PixelOffset](#Image::PixelOffset) ({0, yStart}); size\_t count = (yEnd - yStart) \* nChannels \* image.[resolution](#Image::resolution) \[0\]; pyramid\[i\].[CopyRectIn](#Image::CopyRectIn) ([Bounds2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2i) ({0, yStart}, {image.[resolution](#Image::resolution) \[0\], yEnd}), pstd::span<const float>(image.[p32](#Image::p32).data() + offset, count));

After the loop terminates, we are left with a image to copy into the top level of the image pyramid before it can be returned.

<<Initialize top level of pyramid and return it>>=

pyramid.push\_back([Image](#Image) (origFormat, {1, 1}, image.[channelNames](#Image::channelNames), origEncoding, alloc)); pyramid\[nLevels - 1\].[CopyRectIn](#Image::CopyRectIn) ([Bounds2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2i) ({0, 0}, {1, 1}), pstd::span<const float>(image.[p32](#Image::p32).data(), nChannels)); return pyramid;

### B.5.6 Color Encodings

Color spaces often define a *transfer function* that is used to encode color component values that are stored in the color space. Transfer functions date to cathode ray tube (CRT) displays, which had a nonlinear relationship between display intensity and the voltage of the electron gun, which was modeled with a gamma curve. With CRTs, doubling the RGB color components stored at a pixel did not lead to a doubling of displayed intensity, an undesirable nonlinearity at the end of a rendering process that is built on an assumption of linearity. It was therefore necessary to apply *gamma correction* to image pixels using the inverse of the gamma curve so that the image on the screen had a linear relationship between intensity and pixel values.

While modern displays no longer use electron guns, it is still worthwhile to use a nonlinear mapping with colors that are stored in quantized representations (e.g., 8-bit pixel components). One reason to do so is suggested by *Weber’s law*, which is based on the observation that an increase of 1% of a stimulus value (e.g., displayed color) is generally required before a human observer notices a change—this is the *just noticeable difference*. In turn, a pixel encoding that allocates multiple values to invisible differences is inefficient, at least for display. Weber’s law also suggests a power law–based encoding, along the lines of gamma correction.

pbrt does most of its computation using floating-point color values, for which there is no need to apply a color encoding. (And indeed, such an encoding would need to be inverted any time a computation was performed with such a color value.) However, it is necessary to support color encodings to decode color values from non-floating-point image formats like PNG as well as to encode them before writing images in such formats.

The ColorEncoding class defines the ColorEncoding interface, which handles both encoding and decoding color in various ways.

<<ColorEncoding Definitions>>=

class ColorEncoding: public [TaggedPointer](https://pbr-book.org/4ed/Utilities/Containers_and_Memory_Management.html#TaggedPointer) <LinearColorEncoding, sRGBColorEncoding, GammaColorEncoding> { public: << [ColorEncoding Interface](#fragment-ColorEncodingInterface-0) >>

void ToLinear(pstd::span<const uint8\_t> vin, pstd::span<Float> vout) const; void FromLinear(pstd::span<const Float> vin, pstd::span<uint8\_t> vout) const; Float ToFloatLinear(Float v) const; std::string ToString() const; static const [ColorEncoding](#ColorEncoding) Get(const std::string &name, Allocator alloc); static [ColorEncoding](#ColorEncoding) Linear; static [ColorEncoding](#ColorEncoding) sRGB; static void Init(Allocator alloc);

};

The two main methods that ColorEncoding s must provide are ToLinear(), which takes a set of encoded 8-bit color values and converts them to linear Float s, and FromLinear(), which does the reverse. Both of these take buffers of potentially many values to convert at once, which saves dynamic dispatch overhead compared to invoking them for each value independently.

<<ColorEncoding Interface>>=

void ToLinear(pstd::span<const uint8\_t> vin, pstd::span<Float> vout) const; void FromLinear(pstd::span<const Float> vin, pstd::span<uint8\_t> vout) const;

It is sometimes useful to decode values with greater than 8-bit precision (e.g., some image formats like PNG are able to store 16-bit color channels). Such cases are handled by ToFloatLinear(), which takes a single encoded value stored as a Float and decodes it.

<<ColorEncoding Interface>>+=

Float ToFloatLinear(Float v) const;

The LinearColorEncoding class is trivial: it divides 8-bit values by 255 to convert them to Float s and does the reverse to convert back. pbrt also provides GammaColorEncoding, which applies a plain gamma curve of specified exponent. Neither of these are included in the text here.

sRGBColorEncoding implements the encoding specified by the sRGB color space. It combines a linear segment for small values with a power curve for larger ones.

<<ColorEncoding Definitions>>+=

class sRGBColorEncoding { public: <<sRGBColorEncoding Public Methods>>

PBRT\_CPU\_GPU void ToLinear(pstd::span<const uint8\_t> vin, pstd::span<Float> vout) const; PBRT\_CPU\_GPU Float ToFloatLinear(Float v) const; PBRT\_CPU\_GPU void FromLinear(pstd::span<const Float> vin, pstd::span<uint8\_t> vout) const; std::string ToString() const { return "\[ sRGBColorEncoding \]"; }

};

A linear value is converted to an sRGB-encoded value by

The work of conversion is handled by the LinearToSRGB8() function, which is not included here. It uses a rational polynomial approximation to avoid the cost of a std::pow() call in computing the encoded value.

<<ColorEncoding Method Definitions>>=

void sRGBColorEncoding::FromLinear(pstd::span<const Float> vin, pstd::span<uint8\_t> vout) const { for (size\_t i = 0; i < vin.size(); ++i) vout\[i\] = [LinearToSRGB8](#LinearToSRGB8) (vin\[i\]); }

The inverse transformation is

For 8-bit encoded values, the SRGB8ToLinear() function uses a precomputed 256-entry lookup table. A separate SRGBToLinear() uses a rational polynomial approximation for arbitrary floating-point values between 0 and 1.

<<ColorEncoding Method Definitions>>+=

void sRGBColorEncoding::ToLinear(pstd::span<const uint8\_t> vin, pstd::span<Float> vout) const { for (size\_t i = 0; i < vin.size(); ++i) vout\[i\] = SRGB8ToLinear(vin\[i\]); }

The linear and sRGB encodings are widely used in the system, so they are made available via static member variables in the ColorEncoding class.

<<ColorEncoding Interface>>+=

static [ColorEncoding](#ColorEncoding) Linear; static [ColorEncoding](#ColorEncoding) sRGB;