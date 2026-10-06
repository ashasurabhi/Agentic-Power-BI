# Spec: Card (new) + Trend-Arrow Reference Label

Reusable recipe for a headline-measure card with a colored trend-arrow
reference label underneath (e.g. `$7.0M` / `▼ 41.4% vs LY`). Proven on two
measures (Sales, Gross Margin).

## Placeholders

| Placeholder | Example |
|---|---|
| `{{Table}}` | `_Measures` |
| `{{MainMeasure}}` | `Gross Margin TY` |
| `{{VarMeasure}}` | `Gross Margin Var %` |
| `{{ArrowMeasure}}` | `Gross Margin TY vs LY Arrow` |
| `{{LabelText}}` | `vs LY` |
| `{{FieldId}}` | fresh GUID, e.g. `8c956ed8-22bf-46c0-897f-234b790d3c43` |

## Step 1 — Create the two measures (if they don't exist yet)

Via the `powerbi-authoring` modeling MCP, connected to the live Desktop
session (`connection_operations` → `ListLocalInstances` → `Connect`):

```
{{VarMeasure}} = DIVIDE([{{MainMeasure}}] - [{{MainMeasure.LY}}], [{{MainMeasure.LY}}])
  formatString: 0.00%;-0.00%;0.00%

{{ArrowMeasure}} =
  VAR v = [{{VarMeasure}}]
  RETURN IF(v >= 0, UNICHAR(9650) & " " & FORMAT(v, "0.0%"), UNICHAR(9660) & " " & FORMAT(v, "0.0%"))
```

Create both with `measure_operations` (`operation: Create`). This updates
the **live** model immediately (verify with a DAX query) but leaves
`hasUnsavedChanges: true` in Desktop — **ask the user to save (Ctrl+S)**
before reloading the report. Also mirror both measures into the on-disk
TMDL (`definition/tables/_Measures.tmdl`) using the exact `lineageTag`s the
MCP returned, so the file and the live model agree.

## Step 2 — Card visual JSON

Merge into the visual's `objects` (alongside the existing `Data` role
projection for `{{Table}}.{{MainMeasure}}`):

```json
"value": [
  {
    "properties": {
      "labelDisplayUnits": { "expr": { "Literal": { "Value": "1000000D" } } },
      "labelPrecision": { "expr": { "Literal": { "Value": "1L" } } }
    },
    "selector": { "id": "default" }
  }
],
"referenceLabel": [
  {
    "properties": {
      "value": {
        "expr": {
          "Measure": {
            "Expression": { "SourceRef": { "Entity": "{{Table}}" } },
            "Property": "{{ArrowMeasure}}"
          }
        }
      }
    },
    "selector": {
      "data": [{ "dataViewWildcard": { "matchingOption": 0 } }],
      "metadata": "{{Table}}.{{MainMeasure}}",
      "id": "field-{{FieldId}}",
      "order": 0
    }
  }
],
"referenceLabelTitle": [
  {
    "properties": {
      "show": { "expr": { "Literal": { "Value": "true" } } },
      "titleContentType": { "expr": { "Literal": { "Value": "'custom'" } } },
      "titleText": { "expr": { "Literal": { "Value": "'{{LabelText}}'" } } }
    },
    "selector": { "metadata": "{{Table}}.{{MainMeasure}}", "id": "field-{{FieldId}}" }
  }
],
"referenceLabelValue": [
  {
    "properties": {
      "valueFontColor": {
        "solid": {
          "color": {
            "expr": {
              "Conditional": {
                "Cases": [
                  {
                    "Condition": {
                      "Comparison": {
                        "ComparisonKind": 2,
                        "Left": { "Measure": { "Expression": { "SourceRef": { "Entity": "{{Table}}" } }, "Property": "{{VarMeasure}}" } },
                        "Right": { "Literal": { "Value": "0D" } }
                      }
                    },
                    "Value": { "Literal": { "Value": "'#107C10'" } }
                  },
                  {
                    "Condition": {
                      "Comparison": {
                        "ComparisonKind": 3,
                        "Left": { "Measure": { "Expression": { "SourceRef": { "Entity": "{{Table}}" } }, "Property": "{{VarMeasure}}" } },
                        "Right": { "Literal": { "Value": "0D" } }
                      }
                    },
                    "Value": { "Literal": { "Value": "'#D13438'" } }
                  }
                ]
              }
            }
          }
        }
      }
    },
    "selector": { "metadata": "{{Table}}.{{MainMeasure}}", "id": "field-{{FieldId}}" }
  }
],
"referenceLabelLayout": [
  {
    "properties": {
      "style": { "expr": { "Literal": { "Value": "'sentence'" } } },
      "arrangement": { "expr": { "Literal": { "Value": "'rows'" } } }
    },
    "selector": { "id": "default" }
  }
]
```

## Step 3 — Verify

`validate` → Desktop `--status` (must show `hasUnsavedChanges: false`) →
`--reload` → screenshot the page → confirm the card isn't blank and the
arrow is the expected color.

## Gotchas

- `referenceLabel` / `referenceLabelTitle` / `referenceLabelValue` **must**
  use the `data`+`metadata`+`id`+`order` selector above, not `{id:"default"}`.
  Wrong selector ⇒ the **entire card renders blank**, not just the label,
  even though `validate` reports zero errors.
- `metadata` is always the **main** value's queryRef (`{{Table}}.{{MainMeasure}}`),
  never the arrow/variance measure's.
- One `{{FieldId}}` per reference-label row; reuse it across all three
  objects for that row, use a new one to stack another row.
- No need to add the arrow measure to a `Tooltips` query role — the
  `Measure` expression inside `referenceLabel.value` resolves on its own.
