## Model Configuration

### 💰 **Cost-Optimized Model Routing**
*(Primary Rule: Always use the lowest-cost model that meets requirements.)**"

```markdown
| Model                    | Context | Local/Remote | Input Cost ($/M) | Output Cost ($/M) | Notes                  |
|---|---|---|---|---|---|---|
| **Ministral 3**            | 256k   | Local  | Free   | Free          | ✅ Default (FAST)
| **Qwen3-Coder 30B**        | 32k    | Local  | Free   | Free          | ✅ Best PDF/Code
| **DeepSeek Chat**          | 131k   | Remote | $0.27  | $1.10         | 🔬 Best Value (API)
| **DeepSeek Reasoner**     | 131k   | Remote | $0.55  | $2.19         | ✅ Best for Analysis
| **Kimi K2.5** (Legacy)      | 128k   | Remote | $1.10  | $4.40         | Avoid!
| **Qwen7B**                 | 4k     | Local  | Free   | Free          | ✅ Quick Tasks
```

### 🎯 Routing Guide
- **Default All Tasks to Ministral 3** (Free, ~290K context)
- **Use DeepSeek for** (>$5M input tokens, Research/Analysis tasks)
- **Use Local Models First** (95% savings)
```
📊 **Cost Dashboard (Tracking Script):**
- Add `cost_monitor.sh` to track daily spend
- Email alerts at $5, $10, $20 monthly thresholds
- **minimax-m2:cloud** (remote) - 230B for complex analysis
- **minimax-m2.5:cloud** (remote) - 230B minimax-m2.5
- **qwen2.5:7b** (local) - lightweight Qwen
- **glm-4.7-flash:latest** (local) - fast 30B GLM-4.7 flash
- **llama3.2:latest** (local) - 3.2B Llama 3.2
- **minimax-m2:cloud** (remote) - 230 B minimax-m2
- **minimax-m2.5:cloud** (remote) - 230 B minimax-m2.5

### Previous Configuration (Archived)
- Qwen 2.5 7B was primary for cost and latency optimization
- Upgraded to Qwen3 Coder 30B for improved reasoning and code generation
- Added GPT-OSS 20B and DeepSeek R1 14B for expanded model diversity
- Upgraded to Ministral 3 (8.9B) Feb 25, 2026 for speed + native tools

### Background Processing
- **Primary:** Kimi K2.5 for quality foreground tasks
- **Fallback:** Ollama models (Ministral 3, Qwen3-Coder, DeepSeek-R1) for cost savings
- **Cron jobs:** Use Ollama models to minimize API costs
- **Local-first:** Default to Ollama for privacy and speed when quality allows

---

## HTML Email Design

### Core Principles
- **Tables for layout** - Divs break in Outlook
- **Inline CSS only** - External stylesheets ignored
- **600px max width** - Email standard
- **Mobile-first** - 60%+ opens on mobile
- **High contrast** - Dark text (#1f2937) on light (#ffffff)

### Document Structure
```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
</head>
<body style="margin:0; padding:0; background-color:#f3f4f6;">
  <table role="presentation" width="100%" cellspacing="0" cellpadding="0" border="0">
    <tr>
      <td align="center" style="padding:20px;">
        <table role="presentation" width="600" cellspacing="0" cellpadding="0" border="0"
               style="max-width:600px; background-color:#ffffff;">
          <!-- CONTENT -->
        </table>
      </td>
    </tr>
  </table>
</body>
</html>
```

### Table Attributes (Critical)
| Attribute | Value | Purpose |
|-----------|-------|---------|
| `cellspacing="0"` | Zero | Removes cell spacing |
| `cellpadding="0"` | Zero | Removes cell padding |
| `border="0"` | Zero | Removes borders |
| `role="presentation"` | Accessibility | Screen readers ignore layout tables |
| `width="600"` | Fixed | Email standard width |

### CSS That Works
| Property | Support | Example |
|----------|---------|---------|
| `color` | ✅ Universal | `color:#1f2937;` |
| `background-color` | ✅ Universal | `background-color:#ffffff;` |
| `font-family` | ✅ Universal | `font-family:Arial,sans-serif;` |
| `font-size` | ✅ Universal | `font-size:16px;` |
| `padding` | ✅ Universal | `padding:20px;` |
| `text-align` | ✅ Universal | `text-align:center;` |
| `border-radius` | ❌ Outlook | Avoid - use square corners |
| `display:flex` | ❌ Most clients | Avoid - use tables |
| `float` | ⚠️ Limited | Avoid |

### Bulletproof Button
```html
<table cellspacing="0" cellpadding="0" border="0">
  <tr>
    <td bgcolor="#059669" style="border-radius:6px;">
      <a href="#" style="display:inline-block; padding:15px 35px;
         font-size:16px; color:#ffffff; text-decoration:none; font-weight:bold;">
        BUTTON TEXT
      </a>
    </td>
  </tr>
</table>
```

### Color Palette
| Use | Color | Hex |
|-----|-------|-----|
| Background | White | `#ffffff` |
| Background alt | Light gray | `#f3f4f6`, `#f9fafb` |
| Text primary | Near black | `#1f2937`, `#111827` |
| Text secondary | Gray | `#374151`, `#6b7280` |
| Accent/Link | Blue | `#1e40af`, `#2563eb` |
| Success | Green | `#059669` |
| Header text on dark | Light blue | `#bfdbfe` |

### Never Do
- ❌ White text on light backgrounds
- ❌ Blue text on blue backgrounds
- ❌ Tables wider than 600px
- ❌ Border radius on buttons (Outlook breaks)
- ❌ CSS Grid or Flexbox
- ❌ External stylesheets

### Lessons Learned (Feb 2025)
1. **Always test in Outlook mobile** - worst-case renderer
2. **Single column stacks better** than multi-column on phones
3. **Use 2x2 grids for stats** instead of 4-across (overflows)
4. **Inline all styles** - `<style>` tags get stripped
5. **Use `bgcolor` attribute** - CSS background-color fails in Outlook

### Full Template
See `/workspace/templates/email-master.html` (create as needed)

---

## Church Newsletter Email Generation

**Trigger phrase:** "church newsletter" or "BBCC newsletter"

### Process
1. Scrape events from https://www.bbcconline.com/news
2. Visit each event sub-page for:
   - Hero image (from `og:image` or `twitter:image` meta tag)
   - Full description (truncate to 2-3 sentences)
   - **ALL buttons/links** - especially registration, contact, sign-up
3. Create card-based HTML with:
   - Church brand colors (teal `#14a3c2`, dark text `#19191a`)
   - Full-width responsive images (max 600px)
   - Event title, date/time, description
   - **Dual buttons**: "More Info" (always) + "Register/Sign Up" (if found)
4. Output: HTML file as attachment via email

### Required Elements Per Card
- [ ] Hero image (external URL from website)
- [ ] Event title
- [ ] Date/time/location
- [ ] 2-3 sentence description
- [ ] "More Info" button → event page
- [ ] **Registration button** → any signup/contact link found on page

### Technical Specs
- Max width: 600px
- Single column, mobile-first
- Inline CSS only
- No head/body tags (HTML block ready)
- Images: `width:100%; max-width:100%; height:auto;`
- Border radius: 8px on containers
- Button style: `display:inline-block; background-color:#14a3c2; padding:12px 30px;`

### Output Format
Send HTML file as email attachment to suttersaga@gmail.com with brief description of included events.

---

## Church Bulletin Email Generation

**Trigger phrase:** "bulletin email" or "church bulletin"

### Process
1. Extract content from PDF bulletin provided by user
2. Identify sections: "This Week", "Upcoming Events", "Don't Miss Out", "Make Connections/Disciples", "BBCC for Impact"
3. Match events to website (bbcconline.com/news) for linking
4. Generate HTML blocks with:
   - Blue headers (#14a3c2) with emojis
   - Linked events marked with 🔗
   - QR codes converted to clickable buttons
   - Mobile-responsive design

### Critical Lessons Learned (Feb 5, 2026 Test)

#### ❌ What Went Wrong
1. **Sent wrong HTML file** - Included both bulletin AND 8-card event newsletter
2. **HTML comments visible** - `<!-- comments -->` showed in rendered output
3. **Line breaks in style attributes** - `
` in inline CSS caused rendering issues
4. **Wrong content section** - Didn't isolate just the requested HTML block

#### ✅ What Works in Rock RMS
- Div-based layouts work fine
- Inline CSS with single-line styles (no line breaks)
- External images from Webflow CDN
- Emoji link indicators (🔗)
- Gradient backgrounds
- Multiple buttons per event

#### 🔧 Technical Requirements
```
✅ NO HTML comments in output
✅ Single-line style attributes (no \n characters)
✅ Send ONLY the requested HTML block
✅ No <head>, <body> tags (content block only)
✅ All URLs on single lines
✅ Test in Rock RMS before sending final
```

#### 📋 Checklist Before Sending
- [ ] Correct HTML file attached (not mixed with other templates)
- [ ] No HTML comments visible
- [ ] All style attributes on single lines
- [ ] Events matched to website with 🔗 links
- [ ] QR codes converted to buttons with direct URLs
- [ ] Mobile-responsive tested
- [ ] Church brand colors used (#14a3c2 teal)

### Output Format
Send HTML file as email attachment. File should contain ONLY the requested HTML block (no extra templates).