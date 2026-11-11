---
name: skill-tr-write
description: Write a Technical Requirements HTML file (output/TR.html) from the final BR (output/BR.md). Gives the 7-section structure (including a Huawei Cloud solution architecture section with detailed architecture, process, ER and sequence diagrams), the rules and the full self-contained HTML template with Mermaid diagrams. Used by role-tr.
---

# Write a TR (TR.html)

## Steps

1. Read `output/BR.md`.
2. Write one self-contained file `output/TR.html` using the structure and template below.
   Fill every table and diagram from the BR. Do not create a `.docx` and do not leave a draft file.
3. Check the result: valid HTML5, `lang="th"`, all 7 sections present, 4 diagrams present
   (5.1 architecture, 5.2 process, 5.3 ER, 5.4 sequence), Thai text, no customer brand name.

## Rules

1. Every `TR` item comes from a `REQ` in the BR. Write the REQ ID in the "อ้างอิง" column.
2. Every `REQ` has at least one `TR`.
3. Do not invent technology, numbers or systems. Not in the BR -> `[TBC: what is missing]` (in Thai), and add it to the Open Questions section.
4. IDs: `TR-01`, `TR-02` ... Copy numbers, limits and names from the BR exactly.
5. Thai is the primary language. Write all text in Thai: title, headings, table labels and body.
   Keep in English only: IDs (`REQ-01`, `TR-01`, `Q-01`), `[TBC: ...]`, file names, and technical
   terms with no common Thai word (e.g. SKU, Dashboard, API, Excel). Headings may add English in brackets.
6. Never write the customer's company or brand name. Write "ลูกค้า" (the customer). Only TK is named.
7. Keep it short: at most 20 `TR`, 1 line per cell, no long paragraphs.

### Solution architecture section (section 5)

8. Section 5 is a **design proposal (ข้อเสนอ)**. The BR requires **Huawei Cloud**, so choose
   Huawei Cloud products/services only (e.g. ECS, CCE, ELB, WAF, CDN, RDS, DDS, GaussDB, OBS,
   DCS, API Gateway, FunctionGraph, SMN, KMS, CTS, LTS, AOM, CBR). Do not use another cloud or
   non-Huawei systems unless the BR names them.
9. Choose services that fit the BR. If a detail is unknown (region/AZ, instance size, exact
   database engine, network design), write `[TBC: ...]` (in Thai) and add it to Open Questions.
   Do not invent business rules: the diagrams must reflect only the flows, entities and rules in the BR.
10. Draw **four** Mermaid diagrams, each as detailed as the BR allows (about 8-25 lines):
    - 5.1 Architecture overview -> `flowchart TB` with `subgraph` layers (Client / Edge / App /
      Data / Ops-Security), each node labelled with the Huawei Cloud service.
    - 5.2 Process diagram -> `flowchart TD` (or `LR`) covering the main business flow, with
      decision nodes (`{...}`), branch labels, and a start `([...])` / end `([...])`.
    - 5.3 ER diagram for databases -> `erDiagram` with entities, key attributes, and
      relationship cardinality (one-to-many `||--o{`, one-to-one `||--||`, many-to-many `}o--o{`).
    - 5.4 Sequence diagram -> `sequenceDiagram` with `actor`/`participant`, numbered messages,
      replies (`-->>`), and `alt/else/end` for main alternative flows.
11. Mermaid rules: put the source as the text of `<pre class="mermaid">`. Use Thai labels for
    business meaning; keep IDs/table/column names in English. **Never use the characters `<`
    or `&`** inside a Mermaid block (they break HTML). Do not start a node label with `(`
    right after `[` (that means a cylinder shape). Keep labels short.

## Structure (keep these Thai headings and labels)

1. ภาพรวม (Overview) - meta table + 1-2 lines
2. ผู้ใช้งานและสิทธิ์ (Users and Roles) - table
3. ความต้องการเชิงฟังก์ชัน (Functional Requirements) - table (TR id, อ้างอิง, ความต้องการ, เกณฑ์การยอมรับ)
4. ข้อมูล (Data) - table
5. สถาปัตยกรรมโซลูชัน (Solution Architecture) - 5.1..5.5, four diagrams + infrastructure bullets
6. แพลตฟอร์มและการเชื่อมต่อ (Platform and Integrations) - bullets
7. คำถามที่ยังเปิดอยู่ (Open Questions) - table

## Full HTML template (copy, then fill every `(จาก BR)` and diagram)

```html
<!DOCTYPE html>
<html lang="th">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>ชื่อโครงการ - เอกสารความต้องการทางเทคนิค (TR)</title>
<style>
  :root{--navy:#0B3D6B;--navy2:#12507f;--ink:#1f2937;--mute:#64748b;--line:#d7dee8;--head:#E8EEF5;--bg:#eef1f5;--card:#fff;--zebra:#f7f9fc;}
  *{box-sizing:border-box}
  body{margin:0;background:var(--bg);color:var(--ink);font-family:"Tahoma","Sarabun","Segoe UI",system-ui,sans-serif;line-height:1.6}
  .page{max-width:940px;margin:32px auto;background:var(--card);padding:48px 56px;border-radius:10px;box-shadow:0 6px 30px rgba(11,61,107,.08)}
  header.doc{border-bottom:3px solid var(--navy);padding-bottom:16px;margin-bottom:26px}
  header.doc h1{color:var(--navy);font-size:25px;margin:0 0 6px}
  header.doc .sub{color:var(--mute);font-size:13px}
  h2{color:var(--navy);font-size:19px;margin:34px 0 12px;padding-bottom:6px;border-bottom:1px solid var(--line)}
  h3{color:var(--navy2);font-size:15px;margin:24px 0 10px}
  p{margin:8px 0}
  table{width:100%;border-collapse:collapse;margin:10px 0 6px;font-size:14px}
  th,td{border:1px solid var(--line);padding:8px 10px;text-align:left;vertical-align:top}
  th{background:var(--head);color:var(--navy);font-weight:700}
  tbody tr:nth-child(even){background:var(--zebra)}
  ul{padding-left:22px;margin:8px 0}
  .figure{margin:14px 0 24px;padding:16px;border:1px solid var(--line);border-radius:10px;background:#fbfcfe}
  .figure .cap{font-size:13px;color:var(--mute);margin-top:6px;text-align:center}
  pre.mermaid{margin:0;text-align:center;font-size:14px;white-space:pre-wrap}
  footer{margin-top:38px;border-top:1px solid var(--line);padding-top:12px;font-size:12px;color:var(--mute)}
  @media print{body{background:#fff}.page{box-shadow:none;margin:0;max-width:none;padding:0}.figure{break-inside:avoid}}
</style>
</head>
<body>
<div class="page">
  <header class="doc">
    <h1>ชื่อโครงการ - เอกสารความต้องการทางเทคนิค (TR)</h1>
    <div class="sub">รหัสเอกสาร TK-TR-001 &middot; อ้างอิงจาก BR เวอร์ชัน (จาก BR) &middot; เวอร์ชัน 0.1 &middot; วันที่ (วันนี้)</div>
  </header>

  <section>
    <h2>1. ภาพรวม (Overview)</h2>
    <table>
      <tr><th>หัวข้อ</th><th>รายละเอียด</th></tr>
      <tr><td>รหัสเอกสาร</td><td>TK-TR-001</td></tr>
      <tr><td>อ้างอิงจาก</td><td>(รหัสเอกสาร BR) เวอร์ชัน (เวอร์ชัน BR)</td></tr>
      <tr><td>เวอร์ชัน</td><td>0.1</td></tr>
      <tr><td>วันที่</td><td>(วันนี้)</td></tr>
    </table>
    <p>(1-2 บรรทัด: สิ่งที่จะพัฒนา)</p>
  </section>

  <section>
    <h2>2. ผู้ใช้งานและสิทธิ์ (Users and Roles)</h2>
    <table>
      <tr><th>บทบาท</th><th>สิ่งที่ทำได้</th></tr>
      <tr><td>(บทบาท)</td><td>(สิทธิ์ตาม BR)</td></tr>
    </table>
  </section>

  <section>
    <h2>3. ความต้องการเชิงฟังก์ชัน (Functional Requirements)</h2>
    <table>
      <tr><th>รหัส</th><th>อ้างอิง</th><th>ความต้องการ</th><th>เกณฑ์การยอมรับ</th></tr>
      <tr><td>TR-01</td><td>REQ-01</td><td>(ระบบต้อง ... หน้าจอ, ข้อมูลเข้า, เงื่อนไข, ผลลัพธ์)</td><td>(การทดสอบผ่าน/ไม่ผ่าน จาก BR)</td></tr>
    </table>
  </section>

  <section>
    <h2>4. ข้อมูล (Data)</h2>
    <table>
      <tr><th>ข้อมูล</th><th>ฟิลด์หลัก</th></tr>
      <tr><td>(เช่น สินค้า)</td><td>(ฟิลด์ที่ระบุใน BR)</td></tr>
    </table>
  </section>

  <section>
    <h2>5. สถาปัตยกรรมโซลูชัน (Solution Architecture)</h2>

    <h3>5.1 ภาพรวมสถาปัตยกรรม (Architecture Overview)</h3>
    <div class="figure">
      <pre class="mermaid">
%% ออกแบบ flowchart TB เองจาก BR: แบ่ง subgraph เป็นชั้น Client / Edge / App / Data / Ops-Security
%% แต่ละ node คือบริการ Huawei Cloud ที่เลือกให้เหมาะกับ BR ใส่เฉพาะบริการที่ BR ต้องใช้จริง พร้อมลูกศรเชื่อมต่อ
flowchart TB
  TODO["วาดผังสถาปัตยกรรมจาก BR"]
      </pre>
      <div class="cap">ภาพที่ 5.1 แผนผังสถาปัตยกรรมโซลูชันบน Huawei Cloud</div>
    </div>

    <h3>5.2 แผนภาพกระบวนการ (Process Diagram)</h3>
    <div class="figure">
      <pre class="mermaid">
%% ออกแบบ flowchart TD หรือ LR เองจาก BR: ลำดับธุรกิจหลัก มีจุดตัดสินใจ {...} ป้ายแยกสาขา และจุดเริ่ม/จบ
flowchart TD
  TODO["วาดกระบวนการธุรกิจจาก BR"]
      </pre>
      <div class="cap">ภาพที่ 5.2 แผนภาพกระบวนการธุรกิจหลัก</div>
    </div>

    <h3>5.3 แผนภาพ ER (ER Diagram - Databases)</h3>
    <div class="figure">
      <pre class="mermaid">
%% ออกแบบ erDiagram เองจาก BR: entity, attribute หลัก และ cardinality (||--o{ , ||--|| , }o--o{)
erDiagram
  ENTITY_A ||--o{ ENTITY_B : "ความสัมพันธ์"
      </pre>
      <div class="cap">ภาพที่ 5.3 แผนภาพ ER ของฐานข้อมูล</div>
    </div>

    <h3>5.4 แผนภาพลำดับ (Sequence Diagram)</h3>
    <div class="figure">
      <pre class="mermaid">
%% ออกแบบ sequenceDiagram เองจาก BR: actor/participant, ข้อความ numbered, reply (-->>), และ alt/else/end สำหรับทางเลือกหลัก
sequenceDiagram
  autonumber
  actor A as ผู้ใช้
  participant S as ระบบ
  A->>S: การกระทำหลักจาก BR
      </pre>
      <div class="cap">ภาพที่ 5.4 แผนภาพลำดับของกระบวนการหลัก</div>
    </div>

    <h3>5.5 โครงสร้างพื้นฐานบน Huawei Cloud (Huawei Cloud Infrastructure)</h3>
    <ul>
      <li>(ภูมิภาค / โซน (region/AZ) ตาม BR หรือ [TBC: ...])</li>
      <li>(การสำรองข้อมูลประจำวัน ตาม BR)</li>
      <li>(การขยายระบบ / ความพร้อมใช้งาน หรือ [TBC: ...])</li>
    </ul>
  </section>

  <section>
    <h2>6. แพลตฟอร์มและการเชื่อมต่อ (Platform and Integrations)</h2>
    <ul>
      <li>(อุปกรณ์, เบราว์เซอร์, รูปแบบไฟล์ส่งออก, ระบบที่เชื่อมต่อ ตาม BR)</li>
    </ul>
  </section>

  <section>
    <h2>7. คำถามที่ยังเปิดอยู่ (Open Questions)</h2>
    <table>
      <tr><th>รหัส</th><th>คำถาม</th></tr>
      <tr><td>Q-01</td><td>[TBC: ...]</td></tr>
    </table>
  </section>

  <footer>เอกสารนี้สร้างจาก BR ฉบับสมบูรณ์ - ห้ามแก้ไข BR.docx / BR.md</footer>
</div>
<script type="module">
  import mermaid from "https://cdn.jsdelivr.net/npm/mermaid@11/dist/mermaid.esm.min.mjs";
  mermaid.initialize({
    startOnLoad: true,
    securityLevel: "loose",
    theme: "base",
    fontFamily: "Tahoma, Sarabun, Segoe UI, sans-serif",
    themeVariables: {
      primaryColor: "#E8EEF5", primaryBorderColor: "#0B3D6B", primaryTextColor: "#0B3D6B",
      secondaryColor: "#f7f9fc", tertiaryColor: "#ffffff", lineColor: "#0B3D6B",
      nodeBorder: "#0B3D6B", clusterBkg: "#f2f6fb", clusterBorder: "#9db6cf",
      edgeLabelBackground: "#ffffff", fontSize: "15px"
    },
    flowchart: { curve: "basis", htmlLabels: true, nodeSpacing: 46, rankSpacing: 55 },
    sequence: { actorMargin: 60, width: 150, height: 46, boxMargin: 10, mirrorActors: false, useMaxWidth: true },
    er: { useMaxWidth: true, entityPadding: 15 }
  });
</script>
</body>
</html>
```

- The `pre.mermaid` blocks contain only placeholders. **Design each diagram yourself from the BR**
  (do not copy any sample). Keep the same four diagram types and fill them with the BR's own
  flows, entities and Huawei Cloud services.
- Keep the `<script>` Mermaid loader block at the end of the file so diagrams render.
- Diagrams need internet on first open (Mermaid is loaded from a CDN). If offline, the source
  text still shows inside the figure; note this to the user.
