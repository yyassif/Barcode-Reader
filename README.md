# Barcode-Reader
#### Read Barcode from Strings and Generate Barcode Images

We are Intrested in EAN-13: [EAN-13 (Wikipedia)](https://en.wikipedia.org/wiki/International_Article_Number#Binary_encoding_of_data_digits_into_EAN-13_barcode)

The input is a String of length 95 representing the barcode. Each databit (We are dealing with binary data) is displayed by a specified separator/character(mainly underscore or pipe characters) to represent the active bit and space to represent the dead bit. A separator is the bar area. The input may be reversed. You should decode the barcode so that the output was a String of length 13.

In case a barcode does not conform to the specifications, return (None -> Python) or (NULL -> C++).

The input final digit is the checksum. It is calculated from the other 12 digits, but in this mission it may be wrong. You must return (None -> Python) or (NULL -> C++) for this.

* Input: The separator character and the barcode String, (Note: The length of the barcode is fixed into 95 letters otherwise return None).
* Output: The decoded barcode number in a String format of length 13, None otherwise.

* Examples:
```python
barcode_reader('_', "_ _   _ __ _  ___ __  __  _  __ ____ _  ___ _ _ _ __  __ __ __  _    _ _ ___  _  ___ _   _  _ _") => "5901234123457"

barcode_reader('_', "_ _   __ _ _  ___ ____ _   _ __  _ ___  ___ _ _ _ __  __ _  ___ _  ___ _ ___  _  _   _ _    _ _") => "4003994155486"
```
* Finally:
For Python: We implement this into a GUI using pygame that receives the input and generates the corresponding image if Barcode is true otherwise we raise a message-box error.
For C++ I've never dealt with SFML so no clue how to implement it.
