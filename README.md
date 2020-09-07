This tool will help with creating and updating a bloom filter in Redis (using [the ReBloom module](https://github.com/RedisLabsModules/rebloom)) to hold hashes for passwords that have been revealed through data breaches (to prevent users from using these passwords). The dumps are likely primarily sourced from [Troy Hunt's "Pwned Passwords" files](https://haveibeenpwned.com/Passwords).

Specifically, the commands in this tool allow building the bloom filter somewhere else, then the RDB file can be transferred to the production server. Note that it is expected that a separate Redis server instance is running solely for holding this bloom filter. Replacing the RDB file will result in all other keys being lost.

Expected usage of this tool should look something like (on the machine building the bloom filter):

```
python breached_passwords.py init --estimate 350000000
python breached_passwords.py addhashes pwned-passwords-1.0.txt
python breached_passwords.py addhashes pwned-passwords-update-1.txt
```

Then the RDB file can simply be transferred to the production server, overwriting any
previous RDB file.

To check whether a password is in the bloom filter, send a Redis command like `BF.EXISTS <bloom filter key> <SHA-1 hash of password>`.

---

The script contains two hard-coded values for the unix socket path for the Redis server and the key that the Bloom filter is stored under.

Requires the Python packages [click](https://pypi.org/project/click/) and [redis](https://pypi.org/project/redis/).

---

*This tool was originally written as part of the code for [Tildes](https://tildes.net). It's no longer being used there and has been deleted from the project, so I wanted to retain it separately.*
