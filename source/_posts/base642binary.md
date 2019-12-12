---
title: JS实现base64与二进制的互相转换
date: 2019-11-13 11:23:16
tags: base64
---


# JS解析字符串为二进制数

```js
	let str = 'ABCD';
	let result = null;
	
	for(let i = 0; i < str.length; i ++ ) {
	    result += str.charCodeAt(i).toString(2);
	}
	console.log(result);

```

# 二进制转Base64

```js
	function binaryTobase64(code) {
	  let base64Code = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/";
	  let res = '';
	  // 1 bytes = 8bit，6bit 位替换成一个 base64 字符
	  // 所以每 3 bytes 的数据，能成功替换成 4 个 base64 字符
	    
	  // 对不足 24 bit (也就是 3 bytes) 的情况进行特殊处理
	  if (code.length % 24 === 8) {
	    code += '0000';
	    res += '=='
	  }
	  if (code.length % 24 === 16) {
	    code += '00';
	    res += '='
	  }
	
	  let encode = '';
	  // code 按 6bit 一组，转换为
	  for (let i = 0; i < code.length; i += 6) {
	    let item = code.slice(i, i + 6);
	    encode += base64Code[parseInt(item, 2)];
	  }
	  return encode + res;
	}

```

# 字符串转base64

版本比较新的浏览器可以支持：

```js
	let encodedData = window.btoa("this is a example");
	console.log(encodedData); // dGhpcyBpcyBhIGV4YW1wbGU=
	
	let decodeData = window.atob(encodedData);
	console.log(decodeData); // this is a example
```
如果没有btoa及atob函数，需要自己手写实现了：

```js
	function base64encode(text) {
	  let base64Code = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=";
	  let res = '';
	  let i = 0;
	  while (i < text.length) {
	    let char1, char2, char3, enc1, enc2, enc3, enc4;
	    
	    // 三个字符一组，转二进制
	    char1 = text.charCodeAt(i++); 
	    char2 = text.charCodeAt(i++);
	    char3 = text.charCodeAt(i++);
	
	    enc1 = char1 >> 2; // 取第 1 字节的前 6 位
	    
	    // 三个一组处理
	    if (isNaN(char2)) {
	      // 只有 1 字节的时候
	      enc2 = ((char1 & 3) << 4) | (0 >> 4);
	      // 第65个字符用来代替补位的 = 号
	      enc3 = enc4 = 64;
	    } else if (isNaN(char3)) {
	      // 只有 2 字节的时候
	      enc2 = ((char1 & 3) << 4) | (char2 >> 4);
	      enc3 = ((char2 & 15) << 2) | (0 >> 6);
	      enc4 = 64;
	    } else {
	      enc2 = ((char1 & 3) << 4) | (char2 >> 4); // 取第 1 个字节的后 2 位(3 = 11 << 4 = 110000) + 第 2 个字节的前 4 位
	      enc3 = ((char2 & 15) << 2) | (char3 >> 6); // 取第 2 个字节的后 4 位 (15 = 1111 << 2 = 111100) + 第 3 个字节的前 2 位
	      enc4 = char3 & 63; // 取最后一个字节的最后 6 位 (63 = 111111)
	    }
	    
	    // 转base64
	    res += base64Code.charAt(enc1) + base64Code.charAt(enc2) + base64Code.charAt(enc3) + base64Code.charAt(enc4)
	  }
	
	  return res;
	}

```
