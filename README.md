# LLDB Eigen Pretty Printer

LLDB Eigen Pretty Printer for matrices, arrays and quaternions of the [Eigen](http://eigen.tuxfamily.org) library.

## Update For Clion in Widnows
> tested in CLion 2023.3.4

If you want to use this script in windows with clion. Following these steps:
1. copy the `eigen_formatters.py` file to `YOUR_PATH_TO_CLION\bin\lldb\helpers`
2. modify the `jb_lldb_init.py`, and the following lines to the file end
    ```python
    lldb.debugger.HandleCommand('command script import eigen_formatters')
    ```
    this line of code, import the eigen formatters and init it. You can find the 
    formatters is registered by the cmd `type summary list`, the result will like these: 

    ```
    (lldb) type summary list
    -----------------------
    Category: default
    -----------------------
    ^Eigen::Matrix<.*>$:  (skip pointers) (skip references)
          eigen_formatters.eigen_matrix_print(valobj,internal_dict)
    ^Eigen::Array<.*>$:  (skip pointers) (skip references)
          eigen_formatters.eigen_array_print(valobj,internal_dict)
    ^Eigen::Quaternion<.*>$:  (skip pointers) (skip references)
          eigen_formatters.eigen_quaternion_print(valobj,internal_dict)
    ^Eigen::SparseMatrix<.*>$:  (skip pointers) (skip references)
          eigen_formatters.eigen_sparsematrix_print(valobj,internal_dict)
    ```
    Then you can see the eigen formatter is here.
3. Now restart a exe in debug mode, and you can see the eigen variable is correctly interpreted


## Example

### Dense Matrix
#### Real Matrix
```cpp
Eigen::MatrixXd A(3,5);
A<< 1, 2, 3, 4, 5,
    2, 3, 4, 5, 6,
    3, 4, 6, 7, 8;
```

Corresponding output in LLDB

```
(lldb) p A
(Eigen::MatrixXd) $0 = rows: 3, cols: 5
[ 1.00000000e+00  2.00000000e+00  3.00000000e+00  4.00000000e+00  5.00000000e+00;
  2.00000000e+00  3.00000000e+00  4.00000000e+00  5.00000000e+00  6.00000000e+00;
  3.00000000e+00  4.00000000e+00  6.00000000e+00  7.00000000e+00  8.00000000e+00 ]
```
#### Complex Matrix
```cpp
  Eigen::Matrix<std::complex<float>,3,3,Eigen::RowMajor> B = 10*Eigen::Matrix<std::complex<float>,3,3>::Random();
```
Corresponding out in LLDB
```lldb
(lldb) p B
(Eigen::Matrix<std::complex<float>, 3, 3, 1, 3, 3>) $0 = rows: 3, cols: 3
[  8.98654e+00 - 8.60489e+00i  -8.27888e+00 + 5.19907e-01i   3.26454e+00 - 6.15572e+00i;
  -3.02214e+00 + 7.80465e+00i  -9.59954e+00 - 8.71657e+00i  -8.73808e+00 - 8.45965e-01i;
   9.41268e+00 - 5.23440e+00i   7.01840e+00 + 8.04416e+00i   7.95207e-01 - 4.66669e+00i ]
```
### Array

```cpp
  Eigen::Array22d C;
  C<< 1, 0,
      0, 2;
```

Corresponding output in LLDB

```
(lldb) p C
(Eigen::Array22d) $2 = rows: 2, cols: 2
[ 1.00000000e+00  0.00000000e+00;
  0.00000000e+00  2.00000000e+00 ]
```
### Sparse Matrix
#### Real Matrix
```cpp
  Eigen::SparseMatrix<double> sparseA(10,10);
  sparseA.insert(3,1) = 2.0;
  sparseA.insert(3,2) = 4.0;
  sparseA.insert(1,2) = 5.0;
  sparseA.insert(6,9) = -4.5;
  sparseA.insert(7,8) = 3.5;
  sparseA.insert(7,6) = -1.5;
```
Corresponding out in LLDB
```lldb
(lldb) p sparseA
(Eigen::SparseMatrix<double, 0, int>) $0 = rows: 10, cols: 10, nonzeros: 6
{ [1, 2] =  5.00000000e+00, [3, 1] =  2.00000000e+00, [3, 2] =  4.00000000e+00, [6, 9] = -4.50000000e+00, [7, 6] = -1.50000000e+00, [7, 8] =  3.50000000e+00 }
```
#### Complex Matrix
```cpp
  Eigen::SparseMatrix<std::complex<int> > sparseB(5,3);
  sparseB.insert(2,1) = std::complex<int>(3,-2);
  sparseB.insert(4,2) = std::complex<int>(2,-1);
  sparseB.insert(4,0) = std::complex<int>(0,-5);
  sparseB.insert(1,2) = std::complex<int>(1,-1);
```
```lldb
(lldb) p sparseB
(Eigen::SparseMatrix<std::complex<int>, 0, int>) $1 = rows: 5, cols: 3, nonzeros: 4
{ [1, 2] = 1.00000e+00 - 1.00000e+00i, [2, 1] = 3.00000e+00 - 2.00000e+00i, [4, 0] = 0.00000e+00 - 5.00000e+00i, [4, 2] = 2.00000e+00 - 1.00000e+00i }
```
### Quaternion
```cpp
  Eigen::Quaternion<double> q(2,0,1,-3);
  q.normalize();
```
```lldb
(lldb) p q
(Eigen::Quaternion<double, 0>) $0 = { [x] = 0.00000000e+00, [y] = 2.67261242e-01, [z] = -8.01783726e-01, [w] = 5.34522484e-01 }
```
## Installation

```bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/fantaosha/LLDB-Eigen-Pretty-Printer/master/tools/install.sh)"
```

__Manual Installation__

```bash
INSTALL_PATH=~/.lldb-eigen-pretty-printer
git clone https://github.com/fantaosha/LLDB-Eigen-Pretty-Printer.git $INSTALL_PATH
echo 'command script import "'$INSTALL_PATH'/LLDB_Eigen_Pretty_Printer.py"' >> ~/.lldbinit
```

__Uninstallation__

```bash
rm -fr ~/.lldb-eigen-pretty-printer
```

Afterwards remove the `command script import` command in `~/.lldbinit`.

## Acknowledgement

The development of this LLDB Eigen Pretty Printer has referred to [LLDB Eigen Data Formatter](https://github.com/tehrengruber/LLDB-Eigen-Data-Formatter) and [GDB Eigen Data Printer](https://github.com/RLovelett/eigen/tree/master/debug/gdb).

## License

Copyright © 2018 Taosha Fan

Distributed under the GNU GENERAL PUBLIC LICENSE.
