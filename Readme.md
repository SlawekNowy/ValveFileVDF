#Valve Data Format (.vdf) Reader and Writer in C++
Vavle has its own JSON-like data format: [KeyValue, also known as vdf.](https://developer.valvesoftware.com/wiki/KeyValues)
It is used by valve e.g. in game manifests or as SteamCMD output.
This header-only file provides a parser and writer to load and save the given data.

The parser is based on [Boost Spirit](www.boost.org).

##Features:
- read and write vdf data in C++
- buildt-in encodings: `char`  and `wchar_t`
- supports custom character sets
- supports C++ one line comments (`//`) in parsed strings
- `#include`/`#base` keyword (note: searches for files in the current working directoy)
- platform independent (tested only on windows yet)
- header-only
- Supports C++98 (Check c++98 Branch, tests require c++11)

##Requirements
- [Boost Spirit](www.boost.org)
- C++11 (C++98 supported on own branch, interface differs)

##How-To Use
First, you have to include the main file `vdf-Parser.h`.
This file provides several functions and data-structures which are
in the namespace `tyti::vdf`.

All functions and data structures suppoers wide characters.
The wide character data structure is indicated by the commonly known `w`-prefix.
Functions are templates and don't need a prefix.

To read an file, create a stream e.g. `std::ifsteam` or `std::wifstream`
and call the `tyti::vdf::read` function.
```c++
std::ifstream file("PathToMyFile");
tyti::vdf::object root = tyti::vdf::read(file);
```
You can also define a sequence of character defined by a range.
```c++
std::string blob;
...
tyti::vdf::object root = tyti::vdf::read(std::cbegin(blob), std::cend(blob));

//given .vdf below, following holds
//root.name == "name";
//tyti::vdf::object child = root.childs["child0"];
//child.name == "child0";
//std::string k = root.attribs["attrib0"];
//k == "value"
```

The `tyti::vdf::object` is a tree like data structure.
It has its name, some attributes as a pair of `key` and `value`
and its object childs. Below you can see a vdf data structure and how it is stored by naming:
```javascript
"name"
{
    "attrib0" "value" // saved as a pair, first -> key, second -> value
    "#base" "includeFile.vdf" // appends object defined in the file to childs
    "child0"
    {
    ...
    }
    ...
}
```

Given such an object, you can also write it into vdf files via 'tyti::vdf::write':
```c++
tyti::vdf::write(file, object);
```

##Reference
```c++
// classes
  template<typename T>
  basic_object<T>;
  typedef basic_object<char> object;
  typedef basic_object<wchar_t> wobject

  // Reader functions
  /// reads vdf data from the given stream
  template<typename iStreamT, typename charT = typename iStreamT::char_type>
  basic_object<charT> read(iStreamT& inStream, bool *ok = 0) 
  
  /// reads vdf data within the given range
  template<typename IterT, typename charT = typename IterT::value_type>
  basic_object<charT> read(IterT first, IterT last, bool* ok = 0)

  // Writer functions
  /// writes given obj into out in vdf style 
  template<typename oStreamT, typename charT = typename oStreamT::char_type>
  void write(oStreamT& out, const basic_object<charT>& obj)
  

  
```

## License

[MIT License](./LICENSE) © Matthias Möller. Made with ♥ in Germany.
