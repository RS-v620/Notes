#FixedAndFloatingPointRepresentations 

Numbers are represented using 1s and 0s in binary. The following formats are used for different kinds of numbers:
1.** Positive integers** : All the bits are used to describe the number in binary system
2. **Negative Integers** : Three systems are in place - 
	* **Signed magnitude representation** : The most significant bit is reserved for a sign bit, which includes a 1 for negative integers. The rest of the bits represent the magnitude of the number in binary. $$ eg.,14_{10} = 0\,0001110_{2} \text{ in 8-bit representation}$$ $$-14_{10} = 1\,0001110_{2}\;,\text{ the MSB being 1, rest same as 14}$$
	 
	* **Signed 1's complement representation** : MSB is 1 and the rest bits have 1's complement of the binary representation of the magnitude of the number. $$eg., -14_{10} = 1\,1110001_{2}$$
	* **Signed 2's complement** : The entire number is stored as a 2's complement, with the MSB being 1. $$eg., -14_{10}= 1\,1110010_{2}$$
3. **Fractional Numbers** : Two methods are used
	* **Fixed Decimal Representation** : In this case the decimal point is fixed in the register. $1110.0010$ 
	* **Floating Point Representation** : A pair of memory units work together to store number in this format. One unit stores the magnitude and sign, and the other stores the location of the decimal.
4. **Floating Point representation** : The number is divided into 2 memory segments, mantissa and exponent. The number in mantissa is always a fraction with 0 non fractional part. The number in exponent is signed binary.$$mantissa\;\;\;\;\;\;exponent$$ $$01001110\;\;\;\;\;\;000100$$ It is always made sure that the most significant magnitude bit is 1. The MSB of both mantissa and exponent are reserved for sign. The above number is $$(0.1001110)_{2}\times 2^{+4}$$ A floating point number where MSB in mantissa is 1 is said to be normalized. (The bit beside sign bit).