# Basics concepts 

## 1. swap two numbers . 
``` java 
public static void swapNumbers(int a, int b) {
          a=a^b;
          b=a^b;
          a=a^b;
}

```
logic behind is that  
a=a^b   
b=a^b -> b=(a^b)^b -> b=a  
a=a^b -> a=(a^b)^b -> a=b  


## 2. check if ith bit is set or not . 
```java
public static boolean isIthBitSet(int n, int i) {
    return (n & (1 << i)) != 0;
}
```
logic  
n=12 -> 1101 ( in binary )  
i=2 ->  ith bit to check   
1<<i -> 1<<2 -> 100 ( in binary )  
n & (1 << i) -> 1100 & 0100 -> 0100 ( in binary )  
so here we are we can see that ith bit only set after << operation rest of the numbers will be zero in that case if we do AND operation then all the number will become "zero" but the ith position that we want to check will be set to 1 so in that way we can decide i'th bit is set or not , just by comparing its zero or not . 


## 3. set ith bit of a number . 
```java
public static int setIthBit(int n, int i) {
    return n | (1 << i);
}
```
similar as above just we are using OR operator so we can unset so for setting i'th bit we use OR and for checking i'th bit is set or not we can use AND operator . 

### 4. unset ith bit of a number .
```java
public static int unsetIthBit(int n, int i) {
    return n & ~(1 << i);
}
```
similar as above just we are using AND operator with NOT operator so we can unset i'th bit .

### 5. toggle ith bit of a number .
```java
public static int toggleIthBit(int n, int i) {
    return n ^ (1 << i);
}
```
similar as above just we are using XOR operator so we can toggle i'th bit .

### 6. count number of set bits in a number .
```java
public static int countSetBits(int n) {
    int count = 0;
    while (n > 0) {
        count += (n & 1);
        n >>= 1; // Right shift to check the next bit
    }
    return count;
}
```
logic is simple we are checking if the last bit is set or not using AND operator and then we are right shifting the number to check next bit .

### 7. count number of set bits in a number using Brian Kernighan's Algorithm .
```java
public static int countSetBitsBrianKernighan(int n) {
    int count = 0;
    while (n > 0) {
        n &= (n - 1); // This operation reduces the number of set bits by 1
        count++;
    }
    return count;
}
```

