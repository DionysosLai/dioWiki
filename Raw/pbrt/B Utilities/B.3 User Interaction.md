---
title: "User Interaction"
source: "https://pbr-book.org/4ed/Utilities/User_Interaction"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
## B.3 User Interaction
> ## B.3 用户交互

A number of functions and classes are useful to mediate communicating information to the user.
> 许多函数和类对于调解向用户传达信息非常有用。 In addition to consolidating functionality like printing progress bars, hiding user communication behind a small API like the one here also permits easy modification of the communication mechanisms. For example, if pbrt were embedded in an application that had a graphical user interface, errors might be reported via a dialog box or a routine provided by the parent application. If printf() calls were strewn throughout the system, it would be more difficult to make the two systems work together well.

### B.3.1 Working with Files

A few utility routines make it easy to read and write files from disk. ReadFileContents() returns the contents of a file as a string and ReadDecompressedFileContents() does the same for files that are compressed using the *gzip* algorithm, decompressing them before returning their contents. WriteFileContents() writes the contents of a string to a file. Note that the use of std::string does not impose the requirement that the file contents be text: binary data, including null characters, can be stored in a std::string.

<<File and Filename Function Declarations>>=

std::string ReadFileContents(std::string filename); std::string ReadDecompressedFileContents(std::string filename); bool WriteFileContents(std::string filename, const std::string &contents);

A number of parts of pbrt need to read text files that store floating-point values. Examples include the code that reads measured spectral distributions. The ReadFloatFile() function is available for such uses; it parses text files of white space-separated numbers, returning the values found in a vector. The parsing code ignores all text after a hash mark (#) to the end of its line to allow comments.

<<File and Filename Function Declarations>>+=

std::vector<Float> ReadFloatFile(std::string filename);

### B.3.2 Character Encoding and Unicode

As a rendering system, pbrt is relatively unconcerned with text processing. Yet the scene description is provided as text and the user can configure the system by specifying text command-line arguments, including those that specify scene description files to be parsed and the filename for the final rendered image. Previous versions of pbrt have implicitly assumed that all text is encoded in ASCII, where each character is represented using a single byte. There are 95 printable ASCII characters. In hexadecimal, their values range from, a blank space, to, a tilde.

Adopting ASCII implied that the only letters that can be used in this text are the Latin letters from A to Z. No accented letters were allowed, nor was text written in Chinese, Japanese, or the Devanagari script used for Hindi. (Emoji were also not possible, though we are unsure whether being able to directly render an image named 🚒.exr is a feature worth devoting attention to.)

This version of pbrt uses Unicode ([Unicode Consortium 2020](https://pbr-book.org/4ed/Utilities/Further_Reading.html#cite:Unicode2020)) to represent text. At writing, Unicode allows the representation of nearly 150,000 characters, drawn from scripts that cover a wide variety of languages. (In Unicode, a *script* is a collection of letters and symbols used in the writing system for a language.) Fortunately, most of the code that handles text in pbrt was minimally affected by the change to Unicode, though it is important to understand the underlying principles if one is to read or modify code in pbrt that works with character strings.

Unicode associates a unique numeric *code point* with each character; code points are denoted by U+, where is a hexadecimal integer. The code points for ASCII characters match the ASCII encoding, so “ ~ ” corresponds to both ASCII and U+007e. The letter ü is represented by U+00fc, and the Chinese character 光 is U+5149.

Unicode also defines a number of *encodings* that map code points to sequences of byte values. The simplest is UTF-32, which uses 4 bytes (32 bits) to represent each code point. UTF-32 has the advantage that all code points use the same amount of storage, which makes it easy to perform operations like finding the th code point in a string, though it uses four times more storage for ASCII characters than ASCII does, which is a disadvantage if text is mostly ASCII.

UTF-8 uses a variable number of bytes to represent each code point. ASCII characters are represented with a single byte equal to their code point’s value and thus pure ASCII text is by construction UTF-8 encoded. Code points after U+007f are encoded using 2, 3, or 4 bytes depending on their magnitude. Therefore, finding the th code point requires scanning from the start of a string in the absence of auxiliary data structures. (That operation is not important in pbrt, however.)

UTF-16 occupies an awkward middle ground; it uses two bytes to encode most code points, though it requires four for the ones that cannot fit in two. It offers the disadvantages of UTF-32 (wasted space if text is primarily ASCII), with few advantages in return. UTF-16 is used in the Windows APIs, however, which requires us to be aware of it.

Rather than supporting multiple encodings, pbrt standardizes on UTF-8. It uses std::string s to represent UTF-8-encoded strings, which poses no problems since, in C++, std::string s are just arrays of bytes. It is, however, important to keep in mind that indexing to the th element in a std::string does not necessarily return the th character of the string and that the size() method returns the number of bytes stored in the string and not necessarily the number of characters it holds.

Given the choice of UTF-8, we must ensure that any input from the user in a different encoding is converted to UTF-8 and that any use of strings in calls to system library functions is converted to the character encoding they use. For example, OSX and most versions of Linux now set the system locale to use a UTF-8 encoding. This causes command shells to encode programs’ command-line arguments as UTF-8. On those systems, pbrt therefore assumes that the argv parameters passed to the main() function are already UTF-8 encoded. On Windows, however, command-line arguments are available in ASCII or UTF-16; pbrt takes the latter and converts them to UTF-8.

The GetCommandLineArguments() function handles these details, returning the provided command-line arguments in a vector of std::string s that use the UTF-8 encoding.

<<Command-line Argument Utility Functions>>=

std::vector<std::string> GetCommandLineArguments(char \*argv\[\]);

pbrt provides two functions that convert both ways between the UTF-8 and UTF-16 encodings, where strings of 16-bit values, std::u16string, are used for UTF-16. These are both thin wrappers around functionality provided by the C++ standard library.

<<String Utility Function Declarations>>=

std::string UTF8FromUTF16(std::u16string str); std::u16string UTF16FromUTF8(std::string str);

Windows introduces the additional complication of using the type std::wchar\_t for the elements of UTF-16-encoded strings. On Windows, this type is 16 bits, though the C++ standard does not specify its size. Therefore, pbrt provides additional functions on Windows to convert to and from UTF-16-encoded std::wstring s, which store elements using std::wchar\_t.

<<String Utility Function Declarations>>+=

#ifdef PBRT\_IS\_WINDOWS std::wstring WStringFromUTF8(std::string str); std::string UTF8FromWString(std::wstring str); #endif // PBRT\_IS\_WINDOWS

Filenames also require attention. On Linux, filenames can be any string of bytes, other than the forward slash “/”, which separates path components, and U+0000, which is the end of string marker in C. Thus, UTF-8 encoded filenames (slash notwithstanding) are supported with no further effort, though filenames that are not valid UTF-8 strings are also allowed. Both OSX and Windows use Unicode for filenames, with the UTF-8 and UTF-16 encodings, respectively.

Both the [ReadFileContents()](#ReadFileContents) and [WriteFileContents()](#WriteFileContents) functions introduced earlier therefore handle converting filenames to UTF-16 on Windows, allowing callers to directly pass UTF-8 encoded strings to them. pbrt further provides FOpenRead() and FOpenWrite() functions that wrap the functionality of fopen(). On Windows, they perform the UTF-16 filename conversion and then call \_wfopen() in place of fopen().

Few further changes were needed for Unicode support in pbrt thanks to a key component of the UTF-8 design: not only are the ASCII characters represented in UTF-8 with a single byte and with the same value, but it is also guaranteed that no byte used to encode a non-ASCII code point will be equal to an ASCII value. (Effectively, this means that because the high bit of 8-bit ASCII values is unset, the high bit of any byte used for a non-ASCII Unicode character in UTF-8 is always set.)

To see the value of this part of the design of UTF-8, consider parsing the scene description in pbrt. If for example the parser has encountered an opening double quotation mark ", it then copies all subsequent bytes until the closing quote into a std::string and issues an error if a newline is encountered before the closing quote. In UTF-8, the quotation mark U+0022 is encoded as and newline U+000a as. Because the byte values and are not used to encode any other code points, the parser can be oblivious to Unicode, copying bytes into a string just as it did before until it encounters a byte. It makes no difference to the parsing code whether the byte values in the string represent plain ASCII or characters from other scripts.

More generally, because pbrt does not use any non-ASCII characters in the definition of its scene description format, the parser can continue to operate one byte at a time, without being concerned whether each one is part of a multi-byte UTF-8 character.

### B.3.3 Printing and Formatting Strings

Printf() and StringPrintf() respectively provide improvements to C’s printf() and sprintf() functions. Both support all the formatting directives of printf() and sprintf(), but with the following improvements:

- When %f is used, floating-point values are printed out with a sufficient number of digits to exactly specify their value. This is, unfortunately, not the default behavior of C’s routines.
- The %d directive works directly for all integer types; there is no need for additional qualifiers for int64\_t or size\_t values, etc.
- %s can be used for any class that provides a ToString() method, as almost all of pbrt ’s classes do. (It can also be used for std::string s and many of the container classes in the C++ standard library.)

We have found the last of these three capabilities to be particularly useful for debugging and tracing the system’s operation. These functions are implemented in [util/print.h](https://github.com/mmp/pbrt-v4/tree/master/src/pbrt/util/print.h) and [util/print.cpp](https://github.com/mmp/pbrt-v4/tree/master/src/pbrt/util/print.cpp).

StringPrintf() has the added enhancement that it returns its result directly as a std::string, freeing the caller from needing to worry about allocating a sufficient amount of memory for the result.

<<Printing Function Declarations>>=

template <typename... Args> void Printf(const char \*fmt, Args &&... args); template <typename... Args> std::string StringPrintf(const char \*fmt, Args &&... args);

### B.3.4 Error Reporting

A few functions are available for communicating with the user, provided via the files [\[EntityList: util/cmd: -: error.h\]](https://github.com/mmp/pbrt-v4/tree/master/src/pbrt/[EntityList:%20util/cmd:%20-:error.h]) and [util/error.cpp](https://github.com/mmp/pbrt-v4/tree/master/src/pbrt/util/error.cpp). These should be used for things like reporting errors in scene description files or warnings for cases like scene descriptions that lack any light sources. Each of them takes a FileLoc pointer; this is the structure that the parser uses to record which file and line number a particular token is from. These are passed through to object creation routines as the scene description is being initialized so that error messages can include that information.

<<Error Reporting Function Declarations>>=

void Warning(const FileLoc \*loc, const char \*message); void Error(const FileLoc \*loc, const char \*message); \[\[noreturn\]\] void ErrorExit(const FileLoc \*loc, const char \*message);

There are variants of all of these that call [StringPrintf()](#StringPrintf) so that printf -style formatting strings can be used to print the values of additional arguments. Here is the one for Warning():

<<Error Reporting Inline Functions>>=

template <typename... Args> void Warning(const FileLoc \*loc, const char \*fmt, Args &&... args) { Warning(loc, StringPrintf(fmt, std::forward<Args>(args)...).c\_str()); }

For cases where a FileLoc \* is not available, there are corresponding warning and error functions that take just a format string and arguments. (Alternatively, nullptr can be passed for the FileLoc \* to the methods declared above.)

<<Error Reporting Function Declarations>>+=

template <typename... Args> void Warning(const char \*fmt, Args &&... args); template <typename... Args> void Error(const char \*fmt, Args &&... args); template <typename... Args> \[\[noreturn\]\] void ErrorExit(const char \*fmt, Args &&... args);

### B.3.5 Logging

Mechanisms for logging program execution are provided in the files [util/log.h](https://github.com/mmp/pbrt-v4/tree/master/src/pbrt/util/log.h) and [util/log.cpp](https://github.com/mmp/pbrt-v4/tree/master/src/pbrt/util/log.cpp). These are intended to be used for debugging and other programmer-focused tasks; when printed, they include information such as the source file and line number of the logging call, the date and time that it was made, and which thread made it.

The most important of them are LOG\_VERBOSE(), LOG\_ERROR(), and LOG\_FATAL(). Each takes a formatting string with printf -style formatting directives and then a variable number of arguments to provide values. Their implementations all end up calling [StringPrintf()](#StringPrintf), so all the additional capabilities it provides can be used.

Which messages are printed can be controlled by the –log-level command line option to pbrt. The specified logging level is represented with the LogLevel enumeration, an enumerator of which is stored in a global variable. If the –log-file option is used, a FILE \* is opened to store the logging messages.

<<LogLevel Definition>>=

enum class LogLevel { Verbose, Error, Fatal, Invalid };

<<LogLevel Global Variable Declaration>>=

namespace logging { extern LogLevel logLevel; extern FILE \*logFile; } // namespace logging

Here is the implementation of LOG\_VERBOSE(); the other two are similar. There is one trick to note: the macro is carefully written using the short-circuit && operator so that not only does it expand to a single statement, making it safe to use after an if statement without braces, but the arguments after the formatting string are also not evaluated if verbose logging has not been specified. In this way, it is safe to write logging code that calls functions that may do meaningful amounts of computation for the parameter values while not paying the cost for them if their results are unneeded.

<<Logging Macros>>=

#define LOG\_VERBOSE(...) \\ (pbrt::LogLevel::Verbose >= logging::logLevel && \\ (pbrt::Log(LogLevel::Verbose, \_\_FILE\_\_, \_\_LINE\_\_, \_\_VA\_ARGS\_\_), \\ true))

The underlying Log() function handles the details of formatting the log entry and storing logging messages in a buffer in memory during GPU execution; in that case, messages are eventually copied back to the CPU to be printed.

### B.3.6 Assertions and Runtime Error Checking

A few capabilities are provided for checking for unexpected values at runtime, all defined in the file [util/check.h](https://github.com/mmp/pbrt-v4/tree/master/src/pbrt/util/check.h). pbrt uses these in place of the system-provided assert() macro as they provide more information about which values led to assertion failures, when they occur. These should only be used for errors that the system cannot recover from and only for errors that are due to the system’s implementation: errors in user input and such should be detected and reported using the more friendly mechanisms of the [Warning()](#Warning) and [Error()](#Error) functions.

First, CHECK() replaces assert(), issuing a fatal error if the specified condition is not true. A DCHECK() macro, not included here, performs similar functionality, though only in debug builds.

<<CHECK Macro Definitions>>=

#define CHECK(x) (!(!(x) && (LOG\_FATAL("Check failed: %s", #x), true)))

A common use of assertions is to check a relationship between two values (e.g., that they are equal, or that one is strictly less than another). These operations are performed by the following macros, which dispatch to another one that they all share. (There are similarly D -prefixed variants of these for debug builds only.)

<<CHECK Macro Definitions>>+=

#define CHECK\_EQ(a, b) CHECK\_IMPL(a, b, ==) #define CHECK\_NE(a, b) CHECK\_IMPL(a, b,!=) #define CHECK\_GT(a, b) CHECK\_IMPL(a, b, >) #define CHECK\_GE(a, b) CHECK\_IMPL(a, b, >=) #define CHECK\_LT(a, b) CHECK\_IMPL(a, b, <) #define CHECK\_LE(a, b) CHECK\_IMPL(a, b, <=)

There are three things to see in CHECK\_IMPL(). First, it is careful to evaluate the provided expressions only once, storing their values in the va and vb variables. This ensures that they do not introduce unexpected behavior if they are invoked with an expression that includes side effects (e.g., var++). Second, when the check fails, the error message includes not just the source code form of the check, but also the values that caused the failure. This additional information alone is sometimes enough to debug an issue. Finally, it is implemented in terms of a single iteration do / while loop; in this way, it is a single C++ statement and therefore can be used with if statements without braces.

<<CHECK\_IMPL Macro Definition>>=

#define CHECK\_IMPL(a, b, op) \\ do { \\ auto va = a; \\ auto vb = b; \\ if (!(va op vb)) \\ LOG\_FATAL("Check failed: %s " #op " %s with %s = %s, %s = %s",\\ #a, #b, #a, va, #b, vb); \\ } while (false) /\* swallow semicolon \*/

When a CHECK fails, not only is the error message printed, but pbrt also prints a stack trace that shows some context of the program’s state of execution at that point. In addition, the CheckCallbackScope class can be used to provide additional information about the current program state that is printed upon a CHECK failure.

The error handling system keeps a list of active CheckCallbackScope objects. For each one, it calls the provided callback to get an error string if a CHECK fails.

<<CheckCallbackScope Public Methods>>=

CheckCallbackScope(std::function<std::string(void)> callback);

Thus, it might be used as

Point2i currentPixel; /\* Variable that is updated during rendering \*/ CheckCallbackScope callbackScope(\[&\]() { return StringPrintf("The current pixel is %s", currentPixel); }); // Render...

to include the current pixel coordinates in the error output. The expectation is that CheckCallbackScope objects will be stack-allocated, such that when a function returns, for example, then a CheckCallbackScope that it declared will go out of scope and thence be removed from the active callback scopes by its destructor.

Especially in systems that extensively use stochastic sampling, there may be unusual conditions that are allowed to happen rarely, but where their frequent occurrence would be a bug. (One example that comes up in the implementation of microfacet distributions is when the incident and outgoing directions are exactly opposite, in which case the half angle vector is degenerate. The renderer needs to handle this case when it happens, but it should only happen rarely.) pbrt therefore also provides a CHECK\_RARE(freq, cond) macro that takes a maximum frequency of failure and a condition to check. An error is issued at the end of program execution for any of them where the condition occurred too frequently.

### B.3.7 Displaying Images

pbrt supports a simple socket-based protocol that allows it to communicate with external programs that can display images, both on the same machine and on a remote system from the one that pbrt is running on. This is the mechanism that is invoked when the –display-server option is provided on the command line.

If a connection has been made with such a display program, there are a number of functions that make it easy to visualize arbitrary image data using it. This can be especially useful for debugging or for understanding pbrt ’s execution.

DisplayStatic() causes an image of the specified size to be displayed. The number of specified image channel names determines the number of channels in the image. The provided callback will be called repeatedly for tiles of the overall image, where each call is provided a separate buffer for each specified image channel. These buffers should be filled with values for the given tile bounds in scanline order.

<<DisplayServer Function Declarations>>=

void DisplayStatic(std::string title, [Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) resolution, std::vector<std::string> channelNames, std::function<void([Bounds2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2i), pstd::span<pstd::span<Float>>)> getValues);

DisplayDynamic() is similar, but the callback will be called repeatedly during program execution to get the latest values for dynamic data.

void DisplayDynamic(std::string title, [Point2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Points.html#Point2i) resolution, std::vector<std::string> channelNames, std::function<void([Bounds2i](https://pbr-book.org/4ed/Geometry_and_Transformations/Bounding_Boxes.html#Bounds2i), pstd::span<pstd::span<Float>>)> getValues);

There are additional convenience functions that take [Image](https://pbr-book.org/4ed/Utilities/Images.html#Image) s for both static and dynamic display. Their implementations take care of providing the necessary callback routines to copy data from the image to the provided buffers.

<<DisplayServer Function Declarations>>+=

void DisplayStatic(std::string title, const [Image](https://pbr-book.org/4ed/Utilities/Images.html#Image) &image, pstd::optional< [ImageChannelDesc](https://pbr-book.org/4ed/Utilities/Images.html#ImageChannelDesc) > channelDesc = {}); void DisplayDynamic(std::string title, const [Image](https://pbr-book.org/4ed/Utilities/Images.html#Image) &image, pstd::optional< [ImageChannelDesc](https://pbr-book.org/4ed/Utilities/Images.html#ImageChannelDesc) > channelDesc = {});

### B.3.8 Reporting Progress

The [ProgressReporter](#ProgressReporter) class gives the user feedback about how much of a task has been completed and how much longer it is expected to take. For example, implementations of the various [Integrator::Render()](https://pbr-book.org/4ed/Introduction/pbrt_System_Overview.html#Integrator::Render) methods generally use a [ProgressReporter](#ProgressReporter) to show rendering progress. The implementation prints a row of plus signs, the elapsed time, and the estimated remaining time.

<<ProgressReporter Definition>>=

class ProgressReporter { public: << [ProgressReporter Public Methods](#fragment-ProgressReporterPublicMethods-0) >>

ProgressReporter(int64\_t totalWork, std::string title, bool quiet, bool gpu = false); ~ProgressReporter(); void Update(int64\_t num = 1); void Done(); double ElapsedSeconds() const; std::string ToString() const;

private: <<ProgressReporter Private Methods>>

void printBar();

<<ProgressReporter Private Members>>

int64\_t totalWork; std::string title; bool quiet; Timer timer; std::atomic<int64\_t> workDone; std::atomic<bool> exitThread; std::thread updateThread; pstd::optional<float> finishTime; #ifdef PBRT\_BUILD\_GPU\_RENDERER std::vector<cudaEvent\_t> gpuEvents; std::atomic<size\_t> gpuEventsLaunchedOffset; int gpuEventsFinishedOffset; #endif

};

The constructor takes the total number of units of work to be done (e.g., the total number of camera rays that will be traced) and a short string describing the task being performed. If the gpu parameter is true, then execution on the GPU is tracked. In that case, the implementation must handle the fact that CPU and GPU operation is asynchronous, which it does by adding events to the GPU command stream at each Update() call and then periodically determining which events have been completed to report the appropriate degree of progress. See the source code for details.

<<ProgressReporter Public Methods>>=

ProgressReporter(int64\_t totalWork, std::string title, bool quiet, bool gpu = false);

Once the ProgressReporter has been created, each call to its Update() method signifies that one unit of work has been completed. An optional integer value can be passed to indicate that multiple units have been done. A call to Done() indicates that all work has been completed. Finally, the elapsed time since the ProgressReporter was created is available via the ElapsedSeconds() method. This quantity must be tracked for the progress updates and is often useful to have available.

<<ProgressReporter Public Methods>>+=

void Update(int64\_t num = 1); void Done(); double ElapsedSeconds() const;