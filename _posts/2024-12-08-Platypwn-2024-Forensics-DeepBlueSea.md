---
title: "Platypwn 2024 Forensics - Deep Blue Sea"
read_time: false
comments: false
share: false
related: false
categories:
  - CTF
  - International
  - Team
tags:
- Forensic
toc: true
toc_label: "Challenges"
toc_icon: "list"
---

<img src="/assets/images/platypwn24/flag.png" alt="">

Played this CTF together with Re:UN10N Malaysia members. This one particular challenge is hard to solve (for me at that time atleast).

---

## Forensics

---

### Deep Blue Sea
**Description:**<br>
Legendary pirate Bluebeard has travelled far and wide over the Deep Blue Sea and has seen every corner of it. The crew's legendary ship, the Blue Pearl, is just as legendary. As is Bluebeard's famous cutlass, and parrot, and eyepatch, and so on. Almost as legendary is Bluebeard's ancient treasure map, on which parts of the Sea are said to be marked with blots of crimson ink. There, Bluebeard has buried countless treasures over the years. But the biggest treasure can only be found if all other stashes are discovered. Then, the legend goes, the Depth of the Sea at their locations points to where Bluebeard's most enormous treasure lies, buried beneath the waves of the Deep Blue Sea itself.

Or so they say. You, a renowned treasure hunter, have paid a lot for the soggy piece of parchment now lying in front of you. It smells of seaweed and rip-off. You decide that it's time to slowly peel it open and see if the Sea has left over anything of Bluebeard's notes.

**Flag:**
PP{f1L3_F0rm4t5_4r3_a_Tr345uR3_tR0v3}

They gave `treasure-map.ora` file

Make a basic research about .ora and what is it.

<img src="/assets/images/platypwn24/image1.png" alt="">

It mentions about .ora file can be unzip.

```bash
$ unzip treasure-map.ora
Archive:  treasure-map.ora
 extracting: mimetype
 extracting: mergedimage.png
 extracting: Thumbnails/thumbnail.png
warning:  stripped absolute path spec from /data/layer0.png
 extracting: data/layer0.png
warning:  stripped absolute path spec from /data/layer1.png
 extracting: data/layer1.png
 extracting: stack.xml
```

Extracting the .ora file got three important images.
1. layer0.png
2. layer1.png
3. mergedimage.png

By using stegsolve's red plane on layer0.png we can see dots map on the image.

<img src="/assets/images/platypwn24/image2.png" alt="">

Then after trying everything I can, I take a look back at the intentionally long challenge description. The challenge description mentions about "treasure map" and "marked with blots of crimson ink" which I already get. It said "biggest treasure" which I assume the flag can only be obtain if all of them discovered. After a while (one whole day) it got me thinking that the flag maybe scattered one the dots. So to find the location of the dots on the image, we can use coordinates. So I planned to extract the coordinates from layer0.png and map it to layer1.png. The mapping and extracting the "biggest treasure" not only need to use coordinates but need to use pixel value too. So we will be extracting blue channel RGB pixel value.

Then gave my idea to chatGPT to get the script because I'm not good at coding, and refine it myself and got this:

<img src="/assets/images/platypwn24/image3.png" alt="">

The initial code is gone but I got the final code without visualise mapping:

```python
from PIL import Image

def extract_red_dots(image):
    """
    Extract coordinates of red dots from an image (where R > 0 and G = B = 0).
    """
    red_dot_coords = []
    width, height = image.size
    pixels = image.load()
    
    for x in range(width):
        for y in range(height):
            r, g, b, _ = pixels[x, y]  # Assuming RGBA
            if r > 0 and g == 0 and b == 0:  # Red dot condition
                red_dot_coords.append((x, y))
    
    return red_dot_coords

def decode_blue_channel_dynamically(red_dot_coords, layer1_image):
    """
    Dynamically decode the blue channel values at red dot coordinates in layer1, 
    ordered left-to-right, top-to-bottom.
    """
    # Sort coordinates: first by y (top-to-bottom), then by x (left-to-right)
    sorted_coords = sorted(red_dot_coords, key=lambda coord: (coord[1], coord[0]))

    # Access pixel values for the sorted coordinates in layer1
    layer1_pixels = layer1_image.load()
    blue_channel_values = [layer1_pixels[coord][2] for coord in sorted_coords]

    # Decode blue channel values to ASCII characters
    decoded_message = ''.join(chr(b) for b in blue_channel_values if 32 <= b <= 126)
    return decoded_message, sorted_coords

# File paths
layer0_path = "layer0.png"
layer1_path = "layer1.png"
try:
    # Open layer0 and layer1 images
    layer0_image = Image.open(layer0_path).convert("RGBA")
    layer1_image = Image.open(layer1_path).convert("RGBA")

    # Extract red dot coordinates from layer0
    red_dot_coords = extract_red_dots(layer0_image)

    # Decode the blue channel values dynamically
    decoded_message, sorted_coords = decode_blue_channel_dynamically(red_dot_coords, layer1_image)

    # Print results
    print("Decoded Message:", decoded_message)
    for coord, char in zip(sorted_coords, decoded_message):
        print(f"{coord}: '{char}'")
except Exception as e:
    print(f"Error: {e}")
```

```bash
$ python exploit.py
Decoded Message: PP{f1L3_F0rm4t5_4r3_a_Tr345uR3_tR0v3}
(132, 13): 'P'
(235, 26): 'P'
(317, 39): '{'
(441, 52): 'f'
(12, 66): '1'
(89, 79): 'L'
(199, 92): '3'
(305, 105): '_'
(392, 118): 'F'
(13, 131): '0'
(96, 145): 'r'
(200, 158): 'm'
(318, 171): '4'
(415, 184): 't'
(487, 197): '5'
(74, 211): '_'
(167, 224): '4'
(311, 237): 'r'
(390, 250): '3'
(482, 263): '_'
(93, 277): 'a'
(183, 290): '_'
(260, 303): 'T'
(371, 316): 'r'
(472, 329): '3'
(81, 343): '4'
(183, 356): '5'
(257, 369): 'u'
(351, 382): 'R'
(467, 395): '3'
(70, 409): '_'
(147, 422): 't'
(262, 435): 'R'
(332, 448): '0'
(477, 461): 'v'
(54, 475): '3'
(132, 488): '}'
```

--The End--
{: style="text-align: center;"}