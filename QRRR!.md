### Writeup: QRRR! Challenge (50 Points)

#### Challenge Overview:
The QRRR! challenge involved analyzing a GIF file containing multiple frames, each embedding a QR code. The objective was to decode the QR data from each frame, deduplicate the results, and uncover the flag by applying the appropriate decryption technique. The challenge was worth **50 points**.

#### Step 1: QR Code Decoding
To begin, I developed a Python script using the `PIL` and `pyzbar` libraries to decode the QR codes embedded in the frames of the GIF.

```python
from PIL import Image
from pyzbar.pyzbar import decode

# Path to the GIF file
gif_path = 'QRRR!.gif'

# Load the GIF image
gif_image = Image.open(gif_path)

# List to store all decoded results from the frames
decoded_results = []

# Iterate over each frame of the GIF
def decode_gif_qr(gif_image):
    for frame in range(0, gif_image.n_frames):
        gif_image.seek(frame)  # Go to the specific frame
        frame_image = gif_image.convert('RGB')  # Convert to RGB format
        decoded_objects = decode(frame_image)
        
        if decoded_objects:
            decoded_data = decoded_objects[0].data.decode('utf-8')
            decoded_results.append((frame, decoded_data))
            print(f"Decoded QR data from frame {frame}: {decoded_data}")
    
    if not decoded_results:
        print("No QR code detected in any frames.")
    else:
        print(f"\nDecoded {len(decoded_results)} frames with QR codes.")

# Run the QR decoding on the GIF
decode_gif_qr(gif_image)
```

This script decoded QR data from each of the frames, resulting in multiple outputs that included potential flags and other messages.

#### Step 2: Deduplication
As some frames contained identical QR data, I deduplicated the decoded results to arrive at a unique set of strings:

```
SyntL{q6pnbcxfqcbnfxqEEE!}
SyntL{qbnfxqcbnfxqnfxqcbnf_nbfqwfnvwqbnvfwqbv_nfffnff!}
SyntL{ncfqx7c6jbxf_qbfws6_qbvfwqbvswf!}
SyntL{nfxqcbnxq_66765_fbqvs6666!}
SyntL{nbfcxqcbfnxq_fvbqswfqsfbqs_fbbbbrrrrr___rvswbnvfw!}
SyntL{qstqtsqtvewre_qvfqcs_qsqs!}
SyntL{fxwqxwf_qfbq_qfswfqsf!}
SyntL{vvfqsfq_xqfwxfq_qwfqf!}
SyntL{wuxqysfqyn_fqcbfqcqf_5678765qqqq!}
SyntL{Pbatengf_h_tbg_vggg}
SyntL{ncfxbqcnxfq_vqfnwqs56!}
SyntL{qfstugtstsqfr_ss!}
SyntL{abg_gur_synt_jnyynu!}
SyntL{fnyqnbfxqfncbxqnnbfcxq!}
SyntL{kxqsxbfcxsfb_fqfqfqfqfqqfqf!}
SyntL{abg_gur_synnnnnnnnnnnnttttt!}
SyntL{lnfcqxbcfnxqcbnxqf6!}
SyntL{qbnfcbxqfn7!}
SyntL{bqfc2tq!}
SyntL{bqfc2tqfqsfq!}
```

#### Step 3: ROT Cipher Decryption
The decoded data appeared to be encoded with a ROT cipher. Using a ROT13 decryption tool, I applied the cipher to reveal the following output:

```
FlagY{d6caopksdpoaskdRRR!}
FlagY{doaskdpoaskdaskdpoas_aosdjsaijdoaisjdoi_asssass!}
FlagY{apsdk7p6woks_dosjf6_doisjdoifjs!}
FlagY{askdpoakd_66765_sodif6666!}
FlagY{aospkdposakd_siodfjsdfsodf_sooooeeeee___eifjoaisj!}
FlagY{dfgdgfdgirjer_disdpf_dfdf!}
FlagY{skjdkjs_dsod_dsfjsdfs!}
FlagY{iisdfsd_kdsjksd_djsds!}
FlagY{jhkdlfsdla_sdposdpds_5678765dddd!}
**FlagY{Redacted}**
FlagY{apskodpaksd_idsajdf56!}
FlagY{dsfghtgfgfdse_ff!}
FlagY{not_the_flag_wallah!}
FlagY{saldaoskdsapokdaaospkd!}
FlagY{xkdfkospkfso_sdsdsdsdsddsds!}
FlagY{not_the_flaaaaaaaaaaaaggggg!}
FlagY{yaspdkopsakdpoakds6!}
FlagY{doaspokdsa7!}
FlagY{odsp2gd!}
FlagY{odsp2gdsdfsd!}
```

#### Step 4: Identifying the Flag
From the decrypted data, the correct flag was identified as:
```
FlagY{Redacted}
```

#### Conclusion:
By writing a Python script to decode the QR codes and applying a ROT cipher decryption, I was able to successfully extract the flag. The complete Python script used for decoding and deduplication is as follows:

```python
from PIL import Image
from pyzbar.pyzbar import decode
import codecs  # Importing the codecs module for ROT13 decoding

# Path to the GIF file
gif_path = 'QRRR!.gif'

# Load the GIF image
gif_image = Image.open(gif_path)

# List to store all decoded results from the frames
decoded_results = []

# Iterate over each frame of the GIF
def decode_gif_qr(gif_image):
    for frame in range(0, gif_image.n_frames):
        gif_image.seek(frame)  # Go to the specific frame
        frame_image = gif_image.convert('RGB')  # Convert to RGB format
        decoded_objects = decode(frame_image)
        
        if decoded_objects:
            decoded_data = decoded_objects[0].data.decode('utf-8')
            decoded_results.append((frame, decoded_data))

    if not decoded_results:
        print("No QR code detected in any frames.")
    else:
        print(f"\nDecoded {len(decoded_results)} frames with QR codes.")
        print_output()

def print_output():
    # Deduplicate and sort the results
    unique_results = {}
    for frame, data in decoded_results:
        # Store unique original data
        if data not in unique_results:
            unique_results[data] = frame

    # Print the results in a cleaner format
    print("\nDecoded QR Data (Original and ROT13):")
    for data, frame in unique_results.items():
        rot13_data = decode_rot13(data)
        print(f"Frame {frame} - Original: {data} - ROT13: {rot13_data}")

def decode_rot13(data):
    # Use codecs.encode to apply ROT13 cipher
    return codecs.encode(data, 'rot_13')

# Run the QR decoding on the GIF
decode_gif_qr(gif_image)
```

This challenge was worth **50 points**, and the correct flag was **FlagY{Redacted}**.
