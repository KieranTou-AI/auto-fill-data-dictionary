---
name: auto-fill-data-dictionary
description: Convert copied online PRD text to clean Markdown, confirm the conversion with the user, then extract fields and fill a standardized data dictionary for product requirements. Use when the user asks to create, complete, review, or normalize a PRD data dictionary, identify PRD fields, infer data types (DT), infer operation types (OT), or apply the user's data dictionary abbreviation standard.
---

# Auto Fill Data Dictionary

## Overview

Use this skill to turn PRD content copied from online documents into a structured data dictionary. Always normalize copied PRD text into clean Markdown first, ask the user to confirm the Markdown is correct, and only then identify fields and fill the data dictionary.

Before producing a final data dictionary, load [references/data-dictionary-standard.md](references/data-dictionary-standard.md) and apply its DT and OT definitions exactly.

## Workflow

1. Convert copied PRD text to standard Markdown before extracting fields:
   - Preserve the original heading hierarchy with `#`, `##`, `###`, and deeper headings as needed.
   - Convert tab-separated or visually table-like content into Markdown tables.
   - Convert numbered requirements, lettered subitems, and bullet symbols into Markdown lists.
   - Preserve original requirement numbers, labels, examples, quoted UI text, and Chinese wording.
   - Do not infer missing requirements during conversion.

2. Ask the user to confirm the Markdown conversion. Do not identify fields or produce a data dictionary until the user confirms the converted PRD is correct. If the user requests corrections, revise the Markdown and ask for confirmation again.

3. After confirmation, identify all field-like items in the PRD and show the user the field scope first:
   - Form inputs and configuration items
   - Table/list columns
   - Detail-page display fields
   - Buttons and clickable text
   - Hidden backend fields when explicitly mentioned
   - Status, enum, date/time, numeric, and monitoring fields
   - Ask the user to confirm the field scope before generating the final data dictionary.

4. After the user confirms the field scope, build the data dictionary table with this default format unless the user provides a different template:
   - No.
   - 字段
   - 描述
   - DT
   - OT

5. Infer DT from the PRD wording and examples:
   - Names, IDs, codes, descriptions, labels, text, enum labels: usually `C`
   - Counts, sizes, rates, prices, percentages, durations as numbers: usually `N`
   - Calendar dates without time: `D`
   - Timestamps or date plus time: `DT`
   - Clock time without date: `T`
   - Empty state placeholders: `E`
   - Action controls: `B`

6. Infer OT from behavior:
   - Required form field: include `M`
   - Optional form field: include `O`
   - Editable after save: include `A`
   - Not editable after save: include `FA`
   - Table columns and other fields that are only shown without direct user operation: include `D`
   - Read-only displayed system/monitoring value: include `D`
   - If the same business concept appears both as a filter control and as a table display column, treat them as separate fields when needed: the filter control uses its control OT such as `DRB`, while the table display column uses `D`.
   - If a displayed table column is clickable, use `D/TC`.
   - Clickable text: include `TC`
   - Button: include one of `BL`, `BG`, or `BD` based on visibility/disabled behavior
   - Text input: include `TB`
   - Hidden backend-only field: include `H`
   - Select, radio, switch controls: use `DRB`, `DMB`, `R`, or `S`

7. Preserve uncertainty explicitly. If the PRD does not state a field's type, requiredness, editability, enum values, default value, or button disabled behavior, write `待确认` instead of inventing details.

8. Normalize abbreviations:
   - Use only abbreviations from the standard reference.
   - When one field has multiple operation traits, join OT values with `/`, such as `M/A/TB`.
   - Keep `DT` for the data type 日期时间 and do not confuse it with the umbrella term 数据类型.

## Output Rules

- Keep field names in the user's PRD language.
- Do not add implementation-only database columns unless the PRD mentions them or the user asks to infer hidden fields.
- For buttons, use DT = `B` and OT based on button display behavior.
- For display-only metrics, use OT = `D`; do not mark them as required or optional unless they are also configurable.
- For enum fields, list the enum values in `取值范围/枚举`.
- When using the default 5-column format, put enum values, examples, default values, and uncertainty notes in `描述`.
- For numbers involving float precision, note `float 保留 2 位小数` when applicable.
