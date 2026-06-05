# Mail Flow Analyzer

A single-file forensic email analysis tool. Paste raw email headers and get a complete, plain-English breakdown of what happened to a message — where it went, who handled it, whether authentication passed, where delays occurred, and where to look if something went wrong.

Designed for mail administrators and technically capable users who need fast answers without decoding raw headers by hand.

#### Demo:
https://badbox29.github.io/mail_flow_analyzer/

---

#### Screenshot
![Screenshot](screenshot.png)

---

## Features

### Message Analysis
- **Executive Summary** — sender, recipient, subject, date, delivery time, hop count, detected mail provider, detected third-party gateway, and auth result badges at a glance
- **Mail Flow Story** — plain-English narrative of every hop, distributed into the journey timeline where each sentence belongs
- **Recommended Investigation Point** — single actionable callout identifying where to focus troubleshooting, with system-specific suggested checks; color-coded by urgency (green/blue/amber/red) and collapsed or expanded accordingly
- **Evidence classification** — findings tagged as Direct Evidence, Correlated Evidence, or Heuristic Inference

### Message Journey Timeline
- Hops grouped into logical **delivery phases**: External Sending → Public Routing → Third-Party Mail Gateway → Internal Hand-off → Cloud Mail Filtering → Internal Routing → Mailbox Delivery
- Each phase labeled with a plain-English name and optional story annotation
- Each hop card shows system identification, TLS status, delay pill, and percentage of total transit time
- Collapsible hop cards with full detail: source/destination hostnames, IP, protocol, TLS version, queue ID, timestamp, delay bar, and raw Received header
- **Delivery Outcome phase** — always the final entry, color-coded by delivery status (delivered / bounced / rejected / deferred / incomplete)
- Expand All / Collapse All controls

### Authentication Analysis
- **Authentication Summary** — SPF, DKIM, DMARC, ARC, and Microsoft CompAuth as click-to-expand rows with plain-English explanations written in terms of the *sender's domain*, not your mail system
- **Third-party gateway intelligence** — detects Proofpoint, Mimecast, Barracuda, Cisco ESA, and others; when a gateway is present, auth results are sourced from the gateway's pre-forward verification rather than the boundary server's re-check, with a clear explanation of why
- **Security Analysis** — sender authorization, TLS coverage, DMARC status, DKIM signature, originating IP, HELO hostname, spam verdict; each item includes a plain-English sub-note
- SPF softfail vs. hard fail distinction clearly explained; domain names surfaced throughout

### Infrastructure Detection
- **Mail provider detection** — Microsoft Exchange Online, On-prem Exchange, On-prem Exchange / M365 Hybrid, Google Workspace, Amazon SES, and more
- **Hybrid Exchange detection** — identifies cross-tenant headers and on-prem routing to label hybrid environments correctly
- **Gateway detection** — hostname pattern matching plus vendor-specific header fingerprinting (X-Proofpoint-GUID, X-MC-Unique, X-Barracuda-Spam-Score, X-IronPort-AV, etc.)
- Supports multi-gateway topologies (e.g. Mimecast → Google Workspace, Proofpoint → Exchange Online)

### Microsoft-Specific Analysis
- SCL, BCL, PCL with plain-English verdicts
- Microsoft CompAuth with reason code explanation
- X-Forefront-Antispam-Report expandable detail
- Network Message ID

### Delivery Analysis
- Total delivery time, hop-by-hop delay bars, average hop delay
- Per-hop percentage of total transit time
- Delay classification: Normal / Minor / Warning / Critical

### Delivery Classification
- Automatically classifies headers as: Completed, Bounced / NDR, Rejected, Deferred, or Incomplete
- NDR/bounce detection via DSN headers (Content-Type: message/delivery-status, Action: failed, X-Failed-Recipients, Status: 5xx)
- Incomplete delivery path detection for sender-provided headers from failed sends

### Reporting
- **Print / Export to PDF** — light-theme, print-optimized output via browser print dialog
- **Five report types:**
  - *Executive Summary* — identity block, auth status, most likely finding, investigation point
  - *Full Detail* — all sections
  - *Security Analysis* — authentication summary, security analysis, Microsoft verdicts
  - *Mail Journey* — full phased hop timeline with story callouts and delivery outcome
  - *Custom* — checkbox-selectable sections, all checked by default
- All report types include an unconditional message identity header (from/to/subject/date/provider/gateway/auth)
- Reports are light-themed regardless of app theme; interactive elements stripped

### Additional Tools
- **Raw Header Viewer** — collapsed by default, searchable with keyword highlighting, full copy button
- **Sanitized Sharing** — one-click redaction of email addresses, IPs, Message IDs, tenant IDs, and hostnames for safe sharing in support tickets or forums
- **Compare Mode** — paste two header sets and get a side-by-side diff of fields, authentication results, routing hops, and Microsoft verdicts
- **Dark / Light mode** — defaults to dark; full theme toggle
- **Additional Headers** — collapsed by default; expands to show all non-standard headers with tooltips on known vendor headers

---

## Usage

This is a single static HTML file — no build tools, no dependencies, no server required.

1. Open `index.html` in any modern browser
2. Paste raw email headers into the input field
3. Click **Decode Headers**

To get raw headers from common mail clients:

| Client | How to get raw headers |
|---|---|
| Outlook (desktop) | Open message → File → Properties → Internet headers |
| Outlook Web | Open message → ··· menu → View → View message source |
| Gmail | Open message → ··· menu → Show original |
| Apple Mail | Open message → View → Message → All Headers (or Cmd+Shift+H) |
| Thunderbird | Open message → View → Message Source |

---

## Hosting

The file can be served from any static host — GitHub Pages, Cloudflare Pages, Netlify, or a local web server. No backend is required.

**GitHub Pages example:**
1. Push `index.html` to a repository
2. Go to Settings → Pages → Source → select branch
3. Access at `https://yourusername.github.io/yourrepo/`

---

## Supported Systems

### Third-Party Mail Gateways
Proofpoint, Mimecast, Barracuda, Cisco ESA / IronPort, Abnormal Security, Agari, Forcepoint, Sophos, Trend Micro, Symantec / MessageLabs

### Cloud Mail Platforms
Microsoft Exchange Online, Exchange Online Protection, Microsoft Defender for Office 365, Google Workspace / Gmail, Amazon SES, SendGrid, Mailgun

### On-Premises MTAs
Postfix, Exim, Sendmail, Microsoft Exchange Server

### Hybrid Topologies
Exchange Online + On-prem Exchange (M365 Hybrid), any gateway + any cloud platform combination

---

## Delivery Phase Definitions

| Phase | Description |
|---|---|
| External Sending | Origin server — first hop from the sender |
| Public Routing | Intermediate hops traversing public internet infrastructure |
| Third-Party Mail Gateway | Message scanned by a security gateway (Proofpoint, Mimecast, etc.) |
| Internal Hand-off | Gateway forwarding to the destination mail platform |
| Cloud Mail Filtering | EOP, Google Workspace inbound processing, or equivalent |
| Internal Routing | On-premises Exchange routing after cloud handoff |
| Mailbox Delivery | Final delivery to the recipient mailbox |
| Delivery Outcome | Verdict — delivered, bounced, rejected, deferred, or incomplete |

---

## Notes on Header Reliability

Headers added by servers the sender controls — typically the first one or two hops — can be forged. The most trustworthy headers are those stamped by servers your organization controls, generally the last few hops before delivery. The tool notes this in the interface.

When a third-party gateway is detected, authentication results shown throughout the tool reflect the gateway's pre-forward verification rather than the boundary server's re-check. The boundary re-check failures seen when mail flows through gateways like Proofpoint are a known artifact of that routing pattern and are not spoofing indicators.

---

## Version

v1.5

---

## License

See LICENSE file.
