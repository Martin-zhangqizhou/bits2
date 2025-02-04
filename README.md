/* 
 * CS:APP Data Lab 
 * 
 * <Please put your name and userid here>
 * 
 * bits.c - Source file with your solutions to the Lab.
 *          This is the file you will hand in to your instructor.
 *
 * WARNING: Do not include the <stdio.h> header; it confuses the dlc
 * compiler. You can still use printf for debugging without including
 * <stdio.h>, although you might get a compiler warning. In general,
 * it's not good practice to ignore compiler warnings, but in this
 * case it's OK.  
 */

#if 0
/*
 * Instructions to Students:
 *
 * STEP 1: Read the following instructions carefully.
 */

You will provide your solution to the Data Lab by
editing the collection of functions in this source file.

INTEGER CODING RULES:
 
  Replace the "return" statement in each function with one
  or more lines of C code that implements the function. Your code 
  must conform to the following style:
 
  int Funct(arg1, arg2, ...) {
      /* brief description of how your implementation works */
      int var1 = Expr1;
      ...
      int varM = ExprM;

      varJ = ExprJ;
      ...
      varN = ExprN;
      return ExprR;
  }

  Each "Expr" is an expression using ONLY the following:
  1. Integer constants 0 through 255 (0xFF), inclusive. You are
      not allowed to use big constants such as 0xffffffff.
  2. Function arguments and local variables (no global variables).
  3. Unary integer operations ! ~
  4. Binary integer operations & ^ | + << >>
    
  Some of the problems restrict the set of allowed operators even further.
  Each "Expr" may consist of multiple operators. You are not restricted to
  one operator per line.

  You are expressly forbidden to:
  1. Use any control constructs such as if, do, while, for, switch, etc.
  2. Define or use any macros.
  3. Define any additional functions in this file.
  4. Call any functions.
  5. Use any other operations, such as &&, ||, -, or ?:
  6. Use any form of casting.
  7. Use any data type other than int.  This implies that you
     cannot use arrays, structs, or unions.

 
  You may assume that your machine:
  1. Uses 2s complement, 32-bit representations of integers.
  2. Performs right shifts arithmetically.
  3. Has unpredictable behavior when shifting an integer by more
     than the word size.

EXAMPLES OF ACCEPTABLE CODING STYLE:
  /*
   * pow2plus1 - returns 2^x + 1, where 0 <= x <= 31
   */
  int pow2plus1(int x) {
     /* exploit ability of shifts to compute powers of 2 */
     return (1 << x) + 1;
  }

  /*
   * pow2plus4 - returns 2^x + 4, where 0 <= x <= 31
   */
  int pow2plus4(int x) {
     /* exploit ability of shifts to compute powers of 2 */
     int result = (1 << x);
     result += 4;
     return result;
  }

FLOATING POINT CODING RULES

For the problems that require you to implent floating-point operations,
the coding rules are less strict.  You are allowed to use looping and
conditional control.  You are allowed to use both ints and unsigneds.
You can use arbitrary integer and unsigned constants.

You are expressly forbidden to:
  1. Define or use any macros.
  2. Define any additional functions in this file.
  3. Call any functions.
  4. Use any form of casting.
  5. Use any data type other than int or unsigned.  This means that you
     cannot use arrays, structs, or unions.
  6. Use any floating point data types, operations, or constants.


NOTES:
  1. Use the dlc (data lab checker) compiler (described in the handout) to 
     check the legality of your solutions.
  2. Each function has a maximum number of operators (! ~ & ^ | + << >>)
     that you are allowed to use for your implementation of the function. 
     The max operator count is checked by dlc. Note that '=' is not 
     counted; you may use as many of these as you want without penalty.
  3. Use the btest test harness to check your functions for correctness.
  4. Use the BDD checker to formally verify your functions
  5. The maximum number of ops for each function is given in the
     header comment for each function. If there are any inconsistencies 
     between the maximum ops in the writeup and in this file, consider
     this file the authoritative source.

/*
 * STEP 2: Modify the following functions according the coding rules.
 * 
 *   IMPORTANT. TO AVOID GRADING SURPRISES:
 *   1. Use the dlc compiler to check that your solutions conform
 *      to the coding rules.
 *   2. Use the BDD checker to formally verify that your solutions produce 
 *      the correct answers.
 */


#endif
/* 
 * conditional - same as x ? y : z 
 *   Example: conditional(2,4,5) = 4
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 16
 *   Rating: 3
 */
int conditional(int x, int y, int z) {
  /*若x的取值为0x00000000或0xFFFFFFFF。则答案为(x&y)|(~x&z)。
    若x！=0时，将x=0xFFFFFFFF。x = (!!x)<<31>>31 */
  x = (!!x)<<31>>31;
  return (y&x)|(z&~x);
}
/* 
 * isNonNegative - return 1 if x >= 0, return 0 otherwise 
 *   Example: isNonNegative(-1) = 0.  isNonNegative(0) = 1.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 6
 *   Rating: 3
 */
int isNonNegative(int x) {
  /*判断最高位。若为1返回0，反之 */
  return (~(x>>31)&1);
}
/* 
 * isGreater - if x > y  then return 1, else return 0 
 *   Example: isGreater(4,5) = 0, isGreater(5,4) = 1
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 24
 *   Rating: 3
 */
int isGreater(int x, int y) {
  /*以下情况会返回1。当x>0,y<0，或者当x和y符号相同时mark_ = ~((x^y)>>31)，
    满足x+~y+1>0，x!=y时，equl_ = !!(x^y)=1，x+~y+1>=0时，(~(x+~y+1))>>31 = 0xffffffff */
  int sign_ = ((~x&y)>>31)&1;
  int mark_ = ~((x^y)>>31);
  int equl_ = !!(x^y);
  return sign_ | ((mark_)&(~(x+~y+1))>>31&equl_);
}
/* 
 * absVal - absolute value of x
 *   Example: absVal(-1) = 1.
 *   You may assume -TMax <= x <= TMax
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 10
 *   Rating: 4
 */
int absVal(int x) {
  /*对x处理可以分为两种情况，取反+1，不变+0。
    众所周知，一个数取反可以异或1，不变可以异或0。
    当x<0时，x>>31为0xFFFFFFFF，x^(x>>31)即取反，(x>>31)&1为0x1 */
  return (x^(x>>31))+((x>>31)&1);
}
/*
 * isPower2 - returns 1 if x is a power of 2, and 0 otherwise
 *   Examples: isPower2(5) = 0, isPower2(8) = 1, isPower2(0) = 0
 *   Note that no negative number is a power of 2.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 20
 *   Rating: 4
 */
int isPower2(int x) {
  /*问题转化为，二进制数x中只能出现一个比特位为1的数。还要排除负数与正数。
  我们发现若x==2^n则x-1的特征为[n-1,0]位均为1，所以x&(x-1)=0，
  利用这个性质，再排除特殊情况，便可以获得答案 */
  int ret = ((!(x&(x+~0))) & ((~(x>>31)&(!!x))));
  return ret;;
}
/* 
 * float_neg - Return bit-level equivalent of expression -f for
 *   floating point argument f.
 *   Both the argument and result are passed as unsigned int's, but
 *   they are to be interpreted as the bit-level representations of
 *   single-precision floating point values.
 *   When argument is NaN, return argument.
 *   Legal ops: Any integer/unsigned operations incl. ||, &&. also if, while
 *   Max ops: 10
 *   Rating: 2
 */
unsigned float_neg(unsigned uf) {
  /*考虑特殊情况NaNif((uf&0x7fffffff) > 0x7f800000) return uf;
 return uf ^ 0x80000000。使用异或，将最高符号位取反 */
  if((uf&0x7fffffff) > 0x7f800000) 
  return uf;
  return uf ^ 0x80000000;
}
/* 
 * float_i2f - Return bit-level equivalent of expression (float) x
 *   Result is returned as unsigned int, but
 *   it is to be interpreted as the bit-level representation of a
 *   single-precision floating point values.
 *   Legal ops: Any integer/unsigned operations incl. ||, &&. also if, while
 *   Max ops: 30
 *   Rating: 4
 */
unsigned float_i2f(int x) {
  /*分三部分处理，获取符号位s_ = x&0x80000000，
  若为负数-x，变为正数，则0x80000000为特殊情况分开处理，
  考虑特殊情况，0x0和0x80000000，这两种情况直接返回0和0xcf000000。
  获取最高位的1所在位置，while(!(x&(1<<n_))) n_--;。
  若n_ <= 23这个数需要向左移动到小数部分起始位置（将1放在第23位上），if(n_<=23) x<<=(23-n_);。
  若n_ > 23这个数需要向右移动到小数部分起始位置（将1放在第23位上），
  这时候需要考虑移出部分的舍入问题，若移出部分大于0.5则向上舍入，若小于0.5则向下舍去，若等于0.5则向偶数舍入。
  先将>=0.5情况等同考虑，向上舍入x+=(1<<(n_-24))。若==0.5时，舍入情况若为奇数，
  我们需要-1操作变为偶数，即将最低位的1变为0，x&=(0xffffffff<<(n_-22))，
  若向上舍入时最高位产生了进位，还需要加上进位if(x&(1<<n_)) ;else n_++;。之后拼接浮点数即可 */
  int s_ = x&0x80000000;
  int n_ = 30;
  if(!x) return 0;
  if(x==0x80000000) return 0xcf000000;
  if(s_) x = ~x+1;
  while(!(x&(1<<n_))) n_--;
  if(n_<=23) x<<=(23-n_);
  else{
    x+=(1<<(n_-24));
    if(x<<(55-n_)) ;else x&=(0xffffffff<<(n_-22));
    if(x&(1<<n_))  ;else n_++;
    x >>= (n_-23);
  }
  x=x&0x007fffff;
  n_=(n_+127)<<23;
  return x|n_|s_;;
}
