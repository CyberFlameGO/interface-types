# Interface Types Binary Format


## Interface Types

```
intertype ::= 0x7c                       -> bool
            | 0x7b                       -> s8
            | 0x7a                       -> u8
            | 0x79                       -> s16
            | 0x78                       -> u16
            | 0x77                       -> s32
            | 0x76                       -> u32
            | 0x75                       -> s64
            | 0x74                       -> u64
            | 0x73                       -> float32
            | 0x72                       -> float64
            | 0x71                       -> char
            | 0x70 t:<type>              -> (list t)
            | 0x6f field*:vec(<field>)   -> (record field)*)
            | 0x6e case*:vec(<case>)     -> (variant case)*)
            | 0x6d                       -> string
            | 0x6c t*:vec(<type>)        -> (tuple t*)
            | 0x6b name*:vec(<name>)     -> (flags name*)
            | 0x6a case*:vec(<name>)     -> (enum case*)
            | 0x69 t*:vec(<type>)        -> (union t*)
            | 0x68 t:<type>              -> (optional t)
            | 0x67 t?:<type>? u?:<type>? -> (expected t? (error u)?)
```
Extending [`type` as defined by module-linking](../module-linking/Binary.md#type-definitions):
```
type ::= ...
       | it:<intertype> -> it
```


## Canonical Adapter Function Definitions

Extending [`section` as defined by module-linking](../module-linking/Binary.md#module-definitions):
```
section ::= ...
          | c*:section_7(vec(<canonical>)) => c*
```

```
canonical          ::= 0x02 ft:<typeidx> cfd:<canonical-func-def>     -> (canonical func ft cfd)
canonical-func-def ::= 0x00 opts*:vec(<canonical-option>) x:<funcidx> -> (adapt.import opts* (func x))
                     | 0x01 opts*:vec(<canonical-option>) x:<funcidx> -> (adapt.export opts* (func x))
canonical-options  ::= 0x00 0x00                                      -> string=utf8
                     | 0x00 0x01                                      -> string=utf16
                     | 0x00 0x02                                      -> string=compact-utf16
```


## Component Definitions

Reusing [top-level productions defined by module-linking](../module-linking/Binary.md#module-definitions):
```
component-version  ::= 0x0a 0x00 0x02 0x00
component-preamble ::= <magic> <component-version>
component          ::= <component-preamble> s*:<section>* => (component flatten(s*))
```
