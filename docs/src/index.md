# Overview

This package provides a replacement for [DataArrays.jl](https://github.com/JuliaStats/DataArrays.jl)'s `PooledDataArray` type. Contrary to that type, it supports both arrays without missing values and arrays that allow for the presence of missing values, using the [`Missing`](https://github.com/JuliaData/Missings.jl) type. It is also based on a simpler design by only supporting categorical data, which allows offering more specialized features (like ordering of categories). See the [IndirectArrays.jl](https://github.com/JuliaArrays/IndirectArrays.jl) package for a simpler array type storing data with a small number of values.

The package provides the `CategoricalArray` type designed to hold categorical data (either unordered/nominal or ordered/ordinal) efficiently and conveniently. `CategoricalArray{T}` holds values of type `T`. The `CategoricalArray{Union{T, Missing}}` variant can also contain missing values (represented as `missing`, of the [`Missing`](https://github.com/JuliaData/Missings.jl) type). When indexed, `CategoricalArray{T}` returns special "categorical value" objects (`CategoricalString` for `T = String` or `CategoricalValue{T}` for any other `T`) rather than the original values of type `T`. `CategoricalString` and `CategoricalValue` are simple wrappers around the categorical levels; these types allow very efficient retrieval and comparison of actual values.

Indeed, the main feature of `CategoricalArray` is that it maintains a pool of the levels which can appear in the data. These levels are stored in a specific order: for unordered arrays, this order is only used for pretty printing (e.g. in cross tables or plots); for ordered arrays, it also allows comparing values using the `<` and `>` operators: the comparison is then based on the ordering of levels stored in the array. An ordered `CategoricalValue` can be also compared with a value that when converted is equal to one of the levels of this `CategoricalValue`. Whether an array is ordered can be defined either on construction via the `ordered` argument, or at any time via the `ordered!` function. The `levels` function returns all the levels of `CategoricalArray`, and the `levels!` function can be used to set the levels and their order. Levels are also automatically extended when setting an array element to a level not encountered before. But they are never removed without manual intervention: use the `droplevels!` function for this.

`CategoricalArray{T}` is designed to work with any underlying type `T`, but the most common use case is `T = String`. To streamline operations with string categories, they are handled by the dedicated `CategoricalString` type. It supports all the operations of the generic `CategoricalValue{T}` plus all the operations which work on strings, and is actually a special type of string (i.e. `CategoricalString <: AbstractString`). The only difference from a standard `String` is that comparisons like `<` and `>` are based on the ordering of levels rather than on the lexicographic ordering (this also applies if `CategoricalString` is compared with `AbstractString` other than `CategoricalString`).