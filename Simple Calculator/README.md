## Overview
After opening the website, we are greeted with a simple calculator (obviously). Lets input ```1 + 2``` to the textbox.

```
Result:
1 + 2 = 3
```
As expected, 1 + 2 does equal to 3. The interesting part of this is the url. It has some sort of encrypted input query that I assumed is base64. Let's try to decode the input.

```
import base64

query = "YTozOntpOjA7czozOiJzdW0iO2k6MTtzOjE6IjEiO2k6MjtzOjE6IjIiO30="
query_b64 = base64.b64decode(query)
print(query_b64.decode("ascii"))
```


##### solved by kawijayaa
