# DispatchedTuples.jl

|||
|---------------------:|:----------------------------------------------|
| **Docs Build**       | [![docs build][docs-bld-img]][docs-bld-url]   |
| **Documentation**    | [![dev][docs-dev-img]][docs-dev-url]          |
| **GHA CI**           | [![gha ci][gha-ci-img]][gha-ci-url]           |
| **Code Coverage**    | [![codecov][codecov-img]][codecov-url]        |

[docs-bld-img]: https://github.com/charleskawczynski/DispatchedTuples.jl/workflows/Documentation/badge.svg
[docs-bld-url]: https://github.com/charleskawczynski/DispatchedTuples.jl/actions?query=workflow%3ADocumentation

[docs-dev-img]: https://img.shields.io/badge/docs-dev-blue.svg
[docs-dev-url]: https://charleskawczynski.github.io/DispatchedTuples.jl/dev/

[gha-ci-img]: https://github.com/charleskawczynski/DispatchedTuples.jl/workflows/ci/badge.svg
[gha-ci-url]: https://github.com/charleskawczynski/DispatchedTuples.jl/actions?query=workflow%3Aci

[codecov-img]: https://codecov.io/gh/charleskawczynski/DispatchedTuples.jl/branch/main/graph/badge.svg
[codecov-url]: https://codecov.io/gh/charleskawczynski/DispatchedTuples.jl


DispatchedTuples.jl defines one user-facing type: `DispatchedTuple`, and one user-facing method: `dispatch`. A `DispatchedTuple` is similar to a compile-time dictionary, that uses dispatch for the look-up.

`DispatchedTuple` takes a `Tuple` of `Pair`s, where the `first` field of the `Pair` (the "key") is **an instance of the type you want to dispatch on**. The `second` field of the `Pair` is the quantity (the "value", which can be anything) returned by `dispatch`.

## Example

Here is an example in action

```julia
julia> using DispatchedTuples

julia> struct Foo end;

julia> struct Bar end;

julia> dtup = DispatchedTuple((
               Pair(Foo(), 1),
               Pair(Bar(), 2),
           ));

julia> @show dispatch(dtup, Foo());
dispatch(dtup, Foo()) = (1,)

julia> @show dispatch(dtup, Bar());
dispatch(dtup, Bar()) = (2,)
```

If a `DispatchedTuple` has duplicate keys, then all values are returned in the `Tuple`. Here's an example with duplicate keys:

```julia
julia> using DispatchedTuples

julia> struct Foo end;

julia> struct Bar end;

julia> dtup = DispatchedTuple((
               Pair(Foo(), 1),
               Pair(Foo(), 3),
               Pair(Bar(), 2),
           ));

julia> @show dispatch(dtup, Foo());
dispatch(dtup, Foo()) = (1, 3)

julia> @show dispatch(dtup, Bar());
dispatch(dtup, Bar()) = (2,)
```

The second (optional) argument to `DispatchedTuple` is a default value, which is returned for any unrecognized keys. If the default value is not given, and `dispatch` is called with a key it hasn't seen, an error is thrown.

For convenience, `DispatchedTuple` can alternatively take a `Tuple` of two-element `Tuple`s.
