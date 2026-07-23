# 7.4 Canonicalisation caveat

VP and VC-payload JSON must **preserve key order** (no JCS). JCS reordering shifts BBS+ message indices and breaks proof verification; likewise, the issuance `data` must be byte-identical to MBI's `constructSignData` (G3). Use insertion-ordered maps end-to-end.
