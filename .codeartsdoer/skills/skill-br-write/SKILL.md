---
name: skill-br-write
description: Write a TK BR as a Word file (output/BR.docx) from a customer note. Gives the 6-section outline, the writing rules and the command that builds the .docx. Used by agent-br-writer.
---

# Write a TK BR (BR.docx)

## Steps

1. Read the note in `input/`.
2. Write the BR as Markdown in `output/BR-draft.md`, using the outline below.
3. Run the command in "Build BR.docx" exactly as written (copy all of it). It also deletes the draft.

## Rules

1. Use only facts from the note. Copy names, numbers, dates and amounts exactly.
2. Unknown -> write `[TBC: what is missing]` (in Thai) and add it to section 5.
3. IDs: `REQ-01`, `REQ-02` ... and `Q-01`, `Q-02` ...
4. Thai is the primary language. Write all text in Thai: title, headings, table labels and body.
   Keep in English only: IDs (`REQ-01`, `TR-01`, `Q-01`), `[TBC: ...]`, file names, and technical
   terms with no common Thai word (e.g. SKU, Dashboard, API, Excel). Headings may add English in brackets.
5. No TK internal notes (price, margin, effort).
6. Never write the customer's company or brand name. Write "ลูกค้า" (the customer). Only TK is named.
7. Keep it short: at most 15 `REQ` (merge small ones), 1 line per cell, no long paragraphs.

## Outline (copy and fill - keep these Thai headings and labels)

```
# <ชื่อโครงการ> - เอกสารความต้องการทางธุรกิจ (BR)

## 1. ภาพรวม (Overview)
| หัวข้อ | รายละเอียด |
|---|---|
| รหัสเอกสาร | TK-BR-001 |
| เวอร์ชัน | 0.1 |
| วันที่ | <วันนี้> |
| สถานะ | ฉบับร่าง (Draft) |

<2-3 บรรทัด: ที่มาและเหตุผลของโครงการ>

## 2. ขอบเขต (Scope)
**อยู่ในขอบเขต**
- <รายการ>

**อยู่นอกขอบเขต**
- <รายการ>

## 3. ผู้เกี่ยวข้อง (Stakeholders)
| ชื่อ | องค์กร | บทบาท |
|---|---|---|
| <ชื่อ> | TK หรือ ลูกค้า | <บทบาท> (ผู้อนุมัติ) |

## 4. ความต้องการ (Requirements)
| รหัส | ความต้องการ | วิธีตรวจสอบ |
|---|---|---|
| REQ-01 | ระบบต้อง ... | <การทดสอบผ่าน/ไม่ผ่าน 1 ข้อ> |

## 5. คำถามที่ยังเปิดอยู่ (Open Questions)
| รหัส | คำถาม |
|---|---|
| Q-01 | [TBC: ...] |

## 6. การลงนาม (Sign-off)
| ฝ่าย | ชื่อ | ตำแหน่ง | ลายเซ็น | วันที่ |
|---|---|---|---|---|
| TK | | | | |
| ลูกค้า | | | | |
```

- Exactly one person in section 3 is marked `(ผู้อนุมัติ)`. Unknown -> `[TBC: ผู้อนุมัติ]`.
- Use only: `#`, `##`, `- `, `| tables |`, `**bold**`, plain lines.

## Build BR.docx

Run from the project root. Python 3 only, no install needed.

```bash
python3 - output/BR-draft.md output/BR.docx <<'PY'
import re, sys, zipfile
from xml.sax.saxutils import escape
src, dst = sys.argv[1], sys.argv[2]
NS = 'xmlns:w="http://schemas.openxmlformats.org/wordprocessingml/2006/main"'
F = "Tahoma"
def run(t, b=False):
    out = []
    for i, part in enumerate(re.split(r"\*\*(.+?)\*\*", t)):
        if part:
            rpr = "<w:rPr><w:b/><w:bCs/></w:rPr>" if (b or i % 2) else ""
            out.append(f'<w:r>{rpr}<w:t xml:space="preserve">{escape(part)}</w:t></w:r>')
    return "".join(out)
def p(t, st=None, b=False):
    ppr = '<w:pStyle w:val="%s"/>' % st if st else ""
    return f"<w:p><w:pPr>{ppr}</w:pPr>{run(t, b)}</w:p>"
def table(rows):
    n = max(len(r) for r in rows); w = 9900 // n
    x = ['<w:tbl><w:tblPr><w:tblStyle w:val="Grid"/><w:tblW w:w="5000" w:type="pct"/></w:tblPr><w:tblGrid>'
         + f'<w:gridCol w:w="{w}"/>' * n + "</w:tblGrid>"]
    for i, r in enumerate(rows):
        x.append("<w:tr>")
        for c in r + [""] * (n - len(r)):
            sh = '<w:shd w:val="clear" w:fill="E8EEF5"/>' if i == 0 else ""
            x.append(f'<w:tc><w:tcPr><w:tcW w:w="{w}" w:type="dxa"/>{sh}</w:tcPr>{p(c, b=(i == 0))}</w:tc>')
        x.append("</w:tr>")
    return "".join(x) + "</w:tbl><w:p/>"
body, rows = [], []
for line in open(src, encoding="utf-8").read().splitlines() + [""]:
    s = line.strip()
    if s.startswith("|"):
        cells = [c.strip() for c in s.strip("|").split("|")]
        if not all(re.fullmatch(r":?-{2,}:?", c) for c in cells if c):
            rows.append(cells)
        continue
    if rows:
        body.append(table(rows)); rows = []
    if not s: continue
    if s.startswith("## "): body.append(p(s[3:], "Heading1"))
    elif s.startswith("# "): body.append(p(s[2:], "Title"))
    elif s.startswith(("- ", "* ")): body.append(p("• " + s[2:], "ListBullet"))
    else: body.append(p(s))
def sty(i, name, sz, b="", c="", ppr=""):
    return (f'<w:style w:type="paragraph" w:styleId="{i}"><w:name w:val="{name}"/><w:basedOn w:val="Normal"/>'
            f'<w:pPr>{ppr}</w:pPr><w:rPr>{b}{c}<w:sz w:val="{sz}"/><w:szCs w:val="{sz}"/></w:rPr></w:style>')
B, C = "<w:b/><w:bCs/>", '<w:color w:val="0B3D6B"/>'
border = "".join(f'<w:{s} w:val="single" w:sz="4" w:color="666666"/>' for s in ("top","left","bottom","right","insideH","insideV"))
styles = (f'<?xml version="1.0" encoding="UTF-8"?><w:styles {NS}>'
    f'<w:style w:type="paragraph" w:default="1" w:styleId="Normal"><w:name w:val="Normal"/><w:pPr><w:spacing w:after="80"/></w:pPr>'
    f'<w:rPr><w:rFonts w:ascii="{F}" w:hAnsi="{F}" w:cs="{F}" w:eastAsia="{F}"/><w:sz w:val="22"/><w:szCs w:val="22"/></w:rPr></w:style>'
    + sty("Title", "Title", 36, B, C)
    + sty("Heading1", "heading 1", 28, B, C, '<w:keepNext/><w:spacing w:before="240" w:after="120"/>'
          '<w:pBdr><w:bottom w:val="single" w:sz="8" w:space="1" w:color="0B3D6B"/></w:pBdr>')
    + sty("ListBullet", "List Bullet", 22, ppr='<w:ind w:left="360" w:hanging="360"/>')
    + f'<w:style w:type="table" w:styleId="Grid"><w:name w:val="Table Grid"/><w:tblPr><w:tblBorders>{border}</w:tblBorders>'
      '<w:tblCellMar><w:left w:w="100" w:type="dxa"/><w:right w:w="100" w:type="dxa"/></w:tblCellMar></w:tblPr></w:style></w:styles>')
doc = (f'<?xml version="1.0" encoding="UTF-8"?><w:document {NS}><w:body>{"".join(body)}'
       '<w:sectPr><w:pgSz w:w="11906" w:h="16838"/><w:pgMar w:top="1000" w:right="900" w:bottom="1000" w:left="900" '
       'w:header="500" w:footer="500" w:gutter="0"/></w:sectPr></w:body></w:document>')
R = "http://schemas.openxmlformats.org"
with zipfile.ZipFile(dst, "w", zipfile.ZIP_DEFLATED) as z:
    z.writestr("[Content_Types].xml", f'<?xml version="1.0" encoding="UTF-8"?><Types xmlns="{R}/package/2006/content-types">'
        f'<Default Extension="rels" ContentType="application/vnd.openxmlformats-package.relationships+xml"/>'
        f'<Default Extension="xml" ContentType="application/xml"/>'
        f'<Override PartName="/word/document.xml" ContentType="application/vnd.openxmlformats-officedocument.wordprocessingml.document.main+xml"/>'
        f'<Override PartName="/word/styles.xml" ContentType="application/vnd.openxmlformats-officedocument.wordprocessingml.styles+xml"/></Types>')
    z.writestr("_rels/.rels", f'<?xml version="1.0" encoding="UTF-8"?><Relationships xmlns="{R}/package/2006/relationships">'
        f'<Relationship Id="rId1" Type="{R}/officeDocument/2006/relationships/officeDocument" Target="word/document.xml"/></Relationships>')
    z.writestr("word/_rels/document.xml.rels", f'<?xml version="1.0" encoding="UTF-8"?><Relationships xmlns="{R}/package/2006/relationships">'
        f'<Relationship Id="rId1" Type="{R}/officeDocument/2006/relationships/styles" Target="styles.xml"/></Relationships>')
    z.writestr("word/styles.xml", styles)
    z.writestr("word/document.xml", doc)
print("wrote", dst)
PY
rm -f output/BR-draft.md
```
