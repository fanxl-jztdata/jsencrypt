# Website

http://travistidwell.com/jsencrypt

# Introduction

https://github.com/travist/jsencrypt

Modify the following file to facilitate web front-end use

> lib\lib\jsbn\rsa.js

```javascript
function pkcs1unpad2(d, n) {
    var b = d.toByteArray();
    var i = 0;
    while (i < b.length && b[i] == 0) {
        ++i;
    }
    // commented code the following 3 lines
    // if (b.length - i != n - 1 || b[i] != 2) {
    //     return null;
    // }
    ++i;
    while (b[i] != 0) {
        if (++i >= b.length) {
            return null;
        }
    }
    var ret = "";
    while (++i < b.length) {
        var c = b[i] & 255;
        if (c < 128) {
            // utf-8 decode
            ret += String.fromCharCode(c);
        } else if (c > 191 && c < 224) {
            ret += String.fromCharCode(((c & 31) << 6) | (b[i + 1] & 63));
            ++i;
        } else {
            ret += String.fromCharCode(
                ((c & 15) << 12) | ((b[i + 1] & 63) << 6) | (b[i + 2] & 63),
            );
            i += 2;
        }
    }
    return ret;
}
```

```javascript
RSAKey.prototype.decrypt = function (ctext) {
    var c = parseBigInt(ctext, 16);

    // commented code the following 1 lines，and add the following 1 line
    // var m = this.doPrivate(c);
    // The front-end uses the public key for decryption
    var m = this.doPublic(c);
    if (m == null) {
        return null;
    }
    return pkcs1unpad2(m, (this.n.bitLength() + 7) >> 3);
};
```
