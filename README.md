# LSB Image Steganography

## Overview

This project presents a novel data-hiding technique based on the Least Significant Bit (LSB) technique for digital images. Data hiding is a crucial topic in secret communication, and this paper proposes a lossless data hiding technique using LSB in images. LSB data hiding does not affect the visible properties of the image, making it an effective method for steganography, which is the art and science of hiding the fact that communication is taking place.

Steganography is an important area of research involving a number of applications, and it allows for the embedding of information into cover images (text, video, and image) without causing statistically significant modifications to the cover image.

## Introduction

Image steganography plays a major role in the data hiding method suitable for various applications like satellite communication, the medical field, military, and wireless communication. The rapid growth of internet-based services has made the security and confidentiality of highly sensitive data an issue of supreme importance. To protect information from unauthorized access, data hiding methods such as cryptography, watermarking, and steganography have been developed.

## Objective

The objective of steganography is to hide a secret message within cover media in such a way that others cannot discern the presence of the hidden message. This project implements LSB steganography, where the least significant bits of the image pixels are replaced with the data bits of the secret message.

## Literature Review

The project focuses on building an image steganography system. A few previously published papers and works have been referred to in this field, primarily focusing on techniques for detecting forged images and enhancing data hiding methods.

## Existing Solutions

- **Text Semagrams**: Information is concealed using different methods for presenting the data.
- **Visual Semagrams**: Innocent-looking objects hide the message.
- **Jargon Code**: Utilization of understood language by agreed parties is used in a different way from common usage.
- **Covered Null Cipher**: The payload is concealed into a collection of interlacing instructions agreed upon by the users.
- **Covered Grille Cipher**: A template is used over a cover object that enables the selection of specific characters to constitute the meant message while covering the others.

## System Requirements

### Hardware Requirements
- System 32 bit with 4 GB RAM

### Software Requirements
- **Operating System**: Windows
- **Coding Language**: Python
- **Tools**: Visual Studio Code

## Proposed System

In a grayscale image, each pixel is represented in 8 bits. The least significant bit in a pixel affects the pixel value by only 1, which is used to hide data in the image. This project uses the Least Significant Bit (LSB) steganography technique, where the least significant bits of some or all bytes inside an image are replaced with bits of the secret message. To make this method more secure, the raw data is encrypted before embedding it in the image.

## Implementation

### Source Code

The implementation involves encoding and decoding functions for hiding and retrieving data from the images.

```python
import cv2
import numpy as np
from PIL import Image

def data2binary(data):
    if type(data) == str:
        p = ''.join([format(ord(i), '08b') for i in data])
    elif type(data) == bytes or type(data) == np.ndarray:
        p = [format(i, '08b') for i in data]
    return p

def hidedata(img, data):
    data += "$$"
    d_index = 0
    b_data = data2binary(data)
    len_data = len(b_data)

    for value in img:
        for pix in value:
            r, g, b = data2binary(pix)
            if d_index < len_data:
                pix[0] = int(r[:-1] + b_data[d_index])
                d_index += 1
            if d_index < len_data:
                pix[1] = int(g[:-1] + b_data[d_index])
                d_index += 1
            if d_index < len_data:
                pix[2] = int(b[:-1] + b_data[d_index])
                d_index += 1
            if d_index >= len_data:
                break
    return img

def encode():
    img_name = input("\n Enter image name: ")
    image = cv2.imread(img_name)
    img = Image.open(img_name, 'r')
    w, h = img.size
    data = input("\n Enter message: ")
    if not data:
        raise ValueError("Empty Data")
    enc_img = input("\n Enter encoded image name: ")
    enc_data = hidedata(image, data)
    cv2.imwrite(enc_img, enc_data)
    img1 = Image.open(enc_img, 'r')
    img1 = img1.resize((w, h), Image.LANCZOS)
    img1.save(enc_img, optimize=True, quality=65)

def find_data(img):
    bin_data = ""
    for value in img:
        for pix in value:
            r, g, b = data2binary(pix)
            bin_data += r[-1]
            bin_data += g[-1]
            bin_data += b[-1]
    all_bytes = [bin_data[i:i+8] for i in range(0, len(bin_data), 8)]
    readable_data = ""
    for x in all_bytes:
        readable_data += chr(int(x, 2))
        if readable_data[-2:] == "$$":
            break
    return readable_data[:-2]

def decode():
    img_name = input("\n Enter image name: ")
    image = cv2.imread(img_name)
    msg = find_data(image)
    return msg

def steganography():
    x = 1
    while x != 0:
        print('''\n Image Steganography
1. Encode
2. Decode''')
        u_in = int(input("\n Enter your choice: "))
        if u_in == 1:
            encode()
        else:
            ans = decode()
            print("\n Your message: " + ans)
        x = int(input("\n Enter 1 to continue, otherwise 0: "))

steganography()
