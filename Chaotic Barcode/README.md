We are greeted with a PNG file called `corrupted_barcode.png`. The PNG looks corrupted, therefore we can't see the image nor the preview of the image in the file explorer. Since this is a forensics challenge, my first approach is to use an exif tool to see the actual file extension of the file, and it turned out that this file is actually a PNG. I knew instantly that there must be something wrong with the file structure (which caused the image to be corrupted), so I opened the file in a hex editor, and...

God... what am I looking at here... The file structure is broken inside-out. Just like the challenge description says.

Upon my first inspection, I found some interesting points:
-The header chunk, IHDR, is missing (or purposedly changed with random hex values?)
-The IEND chunk is not at the very end of the hex structure when it should've.
-There is an IDAT chunk with missing chunk values.
-There is also an IDAT chunk that has less values than the specified size.

As we know, a chunk consists of 4 bytes that specifies the size of the chunk, 4 bytes that specifies the chunk name, then the chunk values with the specified size, and lastly 4 bytes of CRC32, an error-detecting piece of code.

After taking another look at the challenge description that says `The most notable is that each of its data are not in a correct place.`, my guess is all the chunks, including the IHDR are entirely out of order. The challenge description also says `Nevertheless, the size and its data integrity check are seems to be fine.`, so I also assumed that the chunk size and CRCs are already in the correct place, which left only the chunk values as the one out of order.

Upon the second inspection, the "deeper" inspection, I noticed some other things:

-There is an IDAT chunk of size 630 that only has 13 chunk values, which looked like an IHDR chunk format. On the other hand, the IHDR chunk of size 13 has 630 values. So, it's obvious that the chunk values of these two chunks are swapped.
-There is an IDAT chunk of size 630 that has zero chunk values (only the CRC, but no values). On the other hand, the IEND chunk, which should have zero chunk values, has 630 chunk values instead. So, it's obvious that these two chunks' values are swapped as well.
-There is an IDAT chunk of size 510 that has 630 chunk values, and there is another IDAT chunk of size 630 that has 510. Swapped as well.

My next approach is to swap back those obvious switched values first, before figuring out the correct order for the chunk values and match the CRCs. The swapping can be done by using a simple hex editor like 010 or hexedit.

After the swap and saving the image, I noticed that both the IHDR chunk and IEND chunk is fixed. We can already preview the image in an image viewer, but it appears that the image is all black. It means that the IDAT chunk values are still out of order, so the full image is still 'broken'. We need to rearrange it somehow.

This can actually be done in Python scripting, but since I'm not too experienced with Python (yet), I decided to find a way to manually rearrange the order of the IDAT chunks. After some researching, I stumbled upon a software called TweakPNG that is able to rearrange chunks. Opening the image in the software causes errors, which states that the CRC of the current chunk is not as what it should've been. With these error messages, I am able to find the correct order of the IDAT chunk without any bruteforcing. After rearranging the IDAT chunks correctly, the image is fixed and it shows a QR code, which when scanned, gave us the flag in plain text.