#GFG
**1.** [**Half Adder**](https://www.geeksforgeeks.org/half-adder-in-digital-logic/) **:** Half Adder is a combinational logic circuit which is designed by connecting one EX-OR gate and one AND gate. The half adder circuit has two inputs: A and B, which add two input digits and generates a carry and a sum. ![](https://media.geeksforgeeks.org/wp-content/uploads/20201008112126/outputonlinepngtools1.png) 

The output obtained from the EX-OR gate is the sum of the two numbers while that obtained by AND gate is the carry. There will be no forwarding of carry addition because there is no logic gate to process that. Thus, this is called the Half Adder circuit. 

**Logical Expression :**

$Sum = A \oplus B$
$Carry = A . B$ 

**Truth Table :**

 ![](https://media.geeksforgeeks.org/wp-content/uploads/20201009113450/outputonlinepngtools3.png) 

**2.** [**Full Adder**](https://www.geeksforgeeks.org/full-adder-in-digital-logic/)**:** Full Adder is the circuit that consists of two EX-OR gates, two AND gates, and one OR gate. Full Adder is the adder that adds three inputs and produces two outputs which consist of two EX-OR gates, two AND gates, and one OR gate. The first two inputs are A and B and the third input is an input carry as C-IN. The output carry is designated as C-OUT and the normal output is designated as S which is SUM.

 ![](https://media.geeksforgeeks.org/wp-content/cdn-uploads/20201020164430/1235.png) 

The equation obtained by the EX-OR gate is the sum of the binary digits. While the output obtained by AND gate is the carry obtained by addition. 

**Truth Table:**

 ![](https://media.geeksforgeeks.org/wp-content/uploads/20201009113442/outputonlinepngtools4.png) 

**Logical Expression :**

SUM = (A XOR B) XOR Cin = (A ⊕ B) ⊕ Cin
CARRY-OUT = A AND B OR Cin(A XOR B) = A.B + Cin(A ⊕ B) 

### Difference between the Half adder and full adder:

|S.No.|Parameters|Half Adder|Full Adder|
|---|---|---|---|
|1.|Description|Half Adder is a combinational logic circuit that adds two 1-bit digits. The half adder produces a sum of the two inputs.|A full adder is a combinational logic circuit that performs an addition operation on three one-bit binary numbers. The full adder produces a sum of the three inputs and carry value.|
|2.|Previous carry|The previous carry is not used.|The previous carry is used.|
|3.|Inputs|In Half adder, there are two input bits ( A, B).|In full adder, there are three input bits (A, B, C-in).|
|4.|Outputs|The generated output is of two bits-Sum and Carry from the input of 2 bits.|The generated output is of two bits-Sum and Carry from the input of 3 bits.|
|5.|Used as|A half adder circuit cannot be used in the same way as a full adder circuit.|A full adder circuit can be used in place of a half adder circuit.|
|6.|Feature|It is simple and easy to implement|The design of a full adder is not as simple as a half adder.|
|7.|Logical Expression|Logical Expression for half adder is : S=a⊕b ; C=a*b.|Logical Expression for Full adder is : S=a⊕b⊕Cin; Cout=(a*b)+(Cin*(a⊕b)).|
|8.|Logic gates|It consists of one EX-OR gate and one AND gate.|It consists of two EX-OR, two AND gates, and one OR gate.|
|9.|Applications|It is used in Calculators, computers, digital measuring devices, etc.|It is used in Multiple bit addition, digital processors, etc.|
|10.|Alternate name|There is no alternate name for half adder.|Full adder is also known as ripple-carry adder.|