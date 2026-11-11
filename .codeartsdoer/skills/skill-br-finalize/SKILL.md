---
name: skill-br-finalize
description: Turn the human-reviewed output/BR.docx into the final Thai output/BR.md (version 1.0, Final) with the command in this skill. Used by agent-br-finalizer.
---

# Finalize a TK BR (BR.docx -> BR.md)

`BR.docx` is the reference. The human already reviewed and edited it.

## Steps

1. Run the command in "Convert BR.docx" exactly as written (copy all of it).
2. In `output/BR.md`, section 1 table (ภาพรวม), set:
   - เวอร์ชัน -> `1.0`
   - สถานะ -> `ฉบับสมบูรณ์ (Final)`
3. Find every `[TBC` in `output/BR.md`. If there are any, list them and ask:
   "Keep these open items in the final BR?" If the user says no, stop and tell
   them to fix `BR.docx` first.
4. Report: file written, number of `REQ` items, open `[TBC]` items (or "none").

## Do not

- Change any wording, ID or value. Only เวอร์ชัน and สถานะ change.
- Translate anything. The BR stays in Thai as the human reviewed it.
- Edit `BR.docx`.

## Convert BR.docx

Run from the project root. Python 3 only, no install needed.

```bash
python3 - output/BR.docx output/BR.md <<'PY'
import re, sys, zipfile
import xml.etree.ElementTree as ET
src, dst = sys.argv[1], sys.argv[2]
W = "{http://schemas.openxmlformats.org/wordprocessingml/2006/main}"
def text(p):
    out = []
    for r in p.iter(W + "r"):
        t = "".join(n.text or "" if n.tag == W + "t" else " " for n in r if n.tag in (W+"t", W+"tab", W+"br"))
        b = r.find(f"{W}rPr/{W}b")
        bold = b is not None and b.get(W + "val", "true") not in ("0", "false")
        out.append(f"**{t}**" if bold and t.strip() else t)
    return re.sub(r"\s+", " ", "".join(out).replace("****", "")).strip()
def para(p):
    t = text(p)
    if not t: return None
    st = p.find(f"{W}pPr/{W}pStyle"); st = (st.get(W + "val") if st is not None else "").lower()
    if st == "title": return "# " + t.strip("*")
    if st.startswith("heading"): return "#" * (int(re.sub(r"\D", "", st) or 1) + 1) + " " + t.strip("*")
    if p.find(f"{W}pPr/{W}numPr") is not None or "list" in st or t.startswith("•"):
        return "- " + t.lstrip("•").strip()
    return t
def table(tbl):
    rows = [[" ".join(x for x in (text(p) for p in tc.iter(W + "p")) if x).replace("|", "\\|")
             for tc in tr.findall(W + "tc")] for tr in tbl.findall(W + "tr")]
    if not rows: return ""
    n = max(len(r) for r in rows); rows = [r + [""] * (n - len(r)) for r in rows]
    lines = ["| " + " | ".join(c.strip("*") for c in rows[0]) + " |", "|" + "---|" * n]
    return "\n".join(lines + ["| " + " | ".join(r) + " |" for r in rows[1:]])
body = ET.fromstring(zipfile.ZipFile(src).read("word/document.xml")).find(W + "body")
blocks = [b for b in (para(e) if e.tag == W + "p" else table(e) if e.tag == W + "tbl" else None for e in body) if b]
out, prev = [], False
for b in blocks:
    bullet = b.startswith("- ")
    if out and not (bullet and prev): out.append("")
    out.append(b); prev = bullet
open(dst, "w", encoding="utf-8").write("\n".join(out) + "\n")
print("wrote", dst)
PY
```
