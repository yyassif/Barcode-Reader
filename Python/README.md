# What is a EAN13 Barcode?
A String Barcode is 95-Characters-length String and 13 digits-length Digit Format.

## Components of a EAN13
| Position | length | Explanation							   |
|:---------|:------:|:-----------------------------------------|
| 0 -> 3   | (3)    | Left guard bar (= 101)				   |
| 3 -> 45  | (42)   | 6 characters. Each character uses 7 bars |
| 45 -> 50 | (5)    | Center bar (= 01010)					   |
| 50 -> 85 | (35)   | 5 characters. Each character uses 7 bars |
| 85 -> 92 | (7)    | 1 character. CheckSum digit 			   |
| 92 -> 95 | (3)    | Right guard bar (= 101)				   |

### Example:
The Separator is set to `'_'` by default.
```python
barcode =
"_ _ __   _ ___  _ ___ __ _  ___   _  _   _ __ _ _ __ __  _  _   _  _   __  __ _ _    __  __ _ _"
 10101100010111001011101101001110001001000101101010110110010010001001000110011010100001100110101
```
|    Barcode    | L.Guard | LEFT                                           | Center.Guard    | RIGHT                                   | CheckSum    | R.Guard |
|:-------------:|:-------:|:----------------------------------------------:|:---------:|:---------------------------------------:|:-----------:|:-------:|
| String Format | `'_ _'` | `' __   _ ___  _ ___ __ _  ___   _  _   _ __'` | `' _ _ '` | `'__ __  _  _   _  _   __  __ _ _    '` | `'__  __ '` | `'_ _'` |
| Binary Format | 101 	  |  011000101110010111011010011100010010001011    | 01010     |  11011001001000100100011001101010000    | 1100110     | 101     |

## Encoding of the digits

### Structure of EAN-13 EO_TABLE (Even Odd Table)
| Index | Binary Representation | Decimal Representation |
|------:|:---------------------:|:----------------------:|
| 0		| `'111111'`			| 63					 |
| 1		| `'110100'`			| 52					 |
| 2		| `'110010'`			| 50					 |
| 3		| `'110001'`			| 49					 |
| 4		| `'101100'`			| 44					 |
| 5		| `'100110'`			| 38					 |
| 6		| `'100011'`			| 35					 |
| 7		| `'101010'`			| 42					 |
| 8		| `'101001'`			| 41					 |
| 9		| `'100101'`			| 37					 |

### Structure of EAN-13 LEFT TABLE
| Index | First SubTable(of index 0)|| Second SubTable(of index 1)||
|------:|:-----------:|:------------:|:-----------:|:-------------:|
|	    | **Binary**  | **Decimal**  | **Binary**  | **Decimal**   |
| 0		| `'100111'`  | 39			 | `'001101'`  | 13			   |
| 1		| `'110011'`  | 51			 | `'011001'`  | 25			   |
| 2		| `'011011'`  | 27			 | `'010011'`  | 19			   |
| 3		| `'100001'`  | 33			 | `'111101'`  | 61			   |
| 4		| `'011101'`  | 29			 | `'100011'`  | 35			   |
| 5		| `'111001'`  | 57			 | `'110001'`  | 49			   |
| 6		| `'000101'`  | 5			 | `'101111'`  | 47			   |
| 7		| `'010001'`  | 17			 | `'111011'`  | 59			   |
| 8		| `'001001'`  | 9			 | `'110111'`  | 55			   |
| 9		| `'010111'`  | 23			 | `'001011'`  | 11			   |

### Structure of EAN-13 RIGHT TABLE
| Index | Binary Representation | Decimal Representation |
|------:|:---------------------:|:----------------------:|
| 0		| `'1110010'`			| 114					 |
| 1		| `'1100110'`			| 102					 |
| 2		| `'1101100'`			| 108					 |
| 3		| `'1000010'`			| 66					 |
| 4		| `'1011100'`			| 92					 |
| 5		| `'1001110'`			| 78					 |
| 6		| `'1010000'`			| 80					 |
| 7		| `'1000100'`			| 68					 |
| 8		| `'1001000'`			| 72					 |
| 9		| `'1110100'`			| 116					 |

## Algorithm of Extraction
1. Translate your String barcode to binary (Normally your barcode would be encoded in binary (spaces and separator) but we have to decode it to zeros and ones).
2. Extract the left 42 characters that construct by slicing them into slices of 7 bars, the left 7 digits of our digit formatted barcode.
	+ For each element of these binary represented 6 slices:
		+ We convert it to decimal representation
		+ Then loop through the table(contains two subtables) of Encoding of the digits of the Left side:
			+ Try for each subatable of LEFT table to find the **index position** of the **decimal digit** otherwise, if the position not found or catch the error and pass to the next **decimal digit** of the remaining 6 slices.
		+ In addition to all that, we have to construct the first digit that comes before all of these 6 digits, but, using the same loop because it's constructed while finding the left 6 digits by checking wether each **digit position** is greater than 0, if so, we save this **bit-part** that indicates that the **decimal digit** is found either in the first subtable(of index 0) or in the second subtable(of index 1) and these are the bits that saving them consecutively and converting its binary representation to decimal, and looking from its equivalent **index position** in the EvenOdd table constructs the first digit.
	+ Hence we've constructed the first digit + the left 6 digits that builds the first 7 digits of our barcode.

3. Extract the right 42 characters that construct by slicing them into slices of 7 bars, the right 6 digits of our digit formatted barcode.
	+ For each element of these binary represented 6 slices:
		+ We convert it to decimal representation
		+ Find the **index position** of the **decimal digit** in the table of Encoding of the digits of the Right side.

	+ Hence we've constructed the Right 6 digits of our barcode.
	+ Note that: **index positions** combined consecutively represent the full barcode, so the left side and right side combined form the the overall digits of the barcode.

4. Verify the CheckSum [Check digit]:
	+ The checksum formula is: 
		+ let x = The Sum of even index digits + 3 * The sum of odd index digits, this includes only the first 12 digits 
		+ So the checksum = (10 - x % 10) % 10
		+ To verify: all you need is to compare checksum you calculated to the 13rd digits of your barcode.

#### Hence barcode is generated.
