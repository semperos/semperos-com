+++
date = "2015-09-02T23:41:29-04:00"
tags = ["gpg","encryption","validation","signature","fingerprint"]
title = "GPG Verify Downloads"

+++

How do you use GPG to verify the integrity of a downloaded file if you have both the file and an accompanying `.asc` file?

<pre>λ: gpg stack-0.1.3.1-x86_64-osx.gz.asc
gpg: assuming signed data in 'stack-0.1.3.1-x86_64-osx.gz'
gpg: Signature made Wed Aug 12 09:26:22 2015 EDT using RSA key ID 9BEFB442
gpg: Good signature from "FPComplete " [unknown]
gpg: WARNING: This key is not certified with a trusted signature!
gpg:          There is no indication that the signature belongs to the owner.
Primary key fingerprint: C570 5533 DA4F 78D8 664B  5DC0 5751 5968 9BEF B442
</pre>

This will work if you already have the GPG key imported. If you need to import the key, then use the `RSA key ID` mentioned in the output and pass it to the `--recv-keys` option first:

<pre>λ: gpg --recv-keys 9BEFB442
</pre>

Running `gpg` as in the first example should now work.

As a final note, you can specify `--verify` for the `gpg` command, in case the version you’re using doesn’t perform verification as its default operation.
