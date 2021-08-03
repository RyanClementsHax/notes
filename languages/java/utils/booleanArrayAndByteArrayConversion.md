# Boolean Array And Byte Array Conversion

## Boolean array to byte array

```java
public byte[] toByteArray(boolean[] bitField) {
    int dataLength = bitField.length % 8 == 0
        ? bitField.length / 8
        : bitField.length / 8 + 1;
    byte[] data = new byte[dataLength];
    for (int i = 0; i < data.length; i++) {
        for (int bit = 0; bit < 8; bit++) {
            if (i * 8 + bit >= bitField.length) {
                continue;
            }
            if (bitField[i * 8 + bit]) {
                data[i] |= (128 >> bit);
            }
        }
    }
    return data;
}
```

## Byte array to boolean array

```java
public boolean[] toBooleanArray(byte[] data) {
    boolean[] bitField = new boolean[bitFieldLength];
    for (int i = 0; i < data.length * 8; i++) {
        if ((data[i / 8] & (1 << (7 - (i % 8)))) > 0)
            bitField[i] = true;
    }

    return bitField;
}
```
