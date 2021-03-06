# Unit Tests for [libigl](https://github.com/libigl/libigl)
[![Build Status](https://travis-ci.org/libigl/libigl-unit-tests.svg?branch=master)](https://travis-ci.org/libigl/libigl-unit-tests)

Get started with

```
git clone --recursive git@github.com:libigl/libigl-unit-tests.git
```

## Dependencies

[googletest](https://github.com/google/googletest) is a submodule


## Build and test

Use `cmake` to generate a `Makefile` that will build _and test_ upon issuing
`make`:

```
mkdir build
cd build
cmake ..
```

Then build and test with

```
make
```

This will first compile the tests and then immediately run the tests. If tests
are succeeding you should see output similar to:

```
[       OK ] Example.Sub1 (1056 ms)
[ RUN      ] Example.Sub2 
[       OK ] Example.Sub2 (4 ms)
[ RUN      ] Example.Sub4
[       OK ] Example.Sub4 (2 ms)
[----------] 3 tests from Example (1062 ms total)
```

## Generating new tests

Many libigl functions act on triangle meshes. To make it easy to add a new test
for a libigl function, we have a script that will output some boilerplate
testing code. So if you issue:

```
cd include/igl/
../../scripts/new.sh myfun
```

This will create a test file `myfun.cpp` containing:

```
#include <test_common.h>
#include <igl/myfun.h>

class myfun : public ::testing::TestWithParam<std::string> {};

TEST_P(myfun, change_to_meaningful_name)
{
  Eigen::MatrixXd V;
  Eigen::MatrixXi F;
  Eigen::SparseMatrix<double> L;
  // Load example mesh: GetParam() will be name of mesh file
  test_common::load_mesh(GetParam(), V, F);
  // ASSERT_EQ(a,b);
  // ASSERT_TRUE(a==b);
  // ASSERT_NEAR(a,b,1e-15)
  // ASSERT_LT(a,1e-12);
}

INSTANTIATE_TEST_CASE_P
(
 all_meshes,
 myfun,
 ::testing::ValuesIn(test_common::all_meshes()),
 test_common::string_test_name
);
```

Add a call to `igl::myfun` and an assertion (e.g., `ASSERT_EQ`) and this will
add a test for _all_ meshes in the `data/` folder. (Should also change
`change_to_meaningful_name` to a meaningful name based on what you're testing).

## Conventions

When naming a test for a function `igl::extra::function_name` use:

```cpp
TEST(extra_function_name, meaning_test_name)
{
  ...
}
```

where `meaning_test_name` could identify the type of test or the type of data
being used.

### Example

The test for `igl::copyleft::cgal::order_facets_around_edges` in
`include/igl/copyleft/cgal/order_facets_around_edges.cpp` is:

```cpp
TEST(copyleft_cgal_order_facets_around_edges, TripletFaces)
{
  ...
}
```

which tests this function on example data containing a triplet of faces.

## Guarantees

None.

(Obviously?) The presence of a unit test here for some function (e.g.,
`igl::cotmatrix`) is not a guarantee or even an endorsement of the notion that
the libigl function `igl::cotmatrix` is bug free or "fully tested" or "heavily
tested" or even "adequately tested".

## Need work?

Some of the most used libigl functions

```bash
grep -hr "^#include \"" ../libigl/include/igl | sed -e 's/\(\.\.\/\)//g' | sort | uniq -c | sort
```

still don't have unit tests.
