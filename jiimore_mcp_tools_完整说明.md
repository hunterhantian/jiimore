# Jiimore MCP 工具完整说明

来源：MCP `tools/list` 原始返回。以下说明和参数描述保留服务端英文原文。

工具数量：17。

## 工具目录

1. `jiimore_get_asin_details` — ASIN Details
2. `jiimore_find_asins_batch` — Find ASINs Batch
3. `jiimore_find_asins_by_keyword` — Find ASINs by Keyword
4. `jiimore_find_same_niche_asins` — Find Same-Niche ASINs
5. `jiimore_get_keyword_rankings` — Get Keyword Rankings
6. `jiimore_search_keywords_batch` — Search Keywords Batch
7. `jiimore_search_keywords_by_asin` — Search Keywords by ASIN
8. `jiimore_search_keywords_by_keyword` — Expand Keywords by Keyword
9. `jiimore_find_niches_by_asin` — Find Niches by ASIN
10. `jiimore_find_niches_by_keyword` — Find Niches by Keyword
11. `jiimore_get_niche_asins` — Get Niche ASINs
12. `jiimore_get_niche_details` — Get Niche Details
13. `jiimore_get_niche_keywords` — Get Niche Keywords
14. `jiimore_expand_aba_keywords_by_keyword` — Expand ABA Keywords by Keyword
15. `jiimore_find_aba_asins_by_keyword` — Find ABA ASINs by Keyword
16. `jiimore_find_similar_asins` — Find Similar ASINs
17. `jiimore_search_aba_keywords` — Search ABA Keywords

## jiimore_get_asin_details

**标题：** ASIN Details

**说明：** Fetch normalized ASIN product details through interface-layer asinInfo. Returns weekly click and conversion metrics, product detail fields, Best Seller Rank data, and Amazon bullet-point highlights. Reviews, trend series, and ASIN niche lists are not requested by this MCP tool; unresolved ASINs are reported in response completeness and warnings.

### 输入参数

```json
{
  "type": "object",
  "properties": {
    "request": {
      "type": "object",
      "properties": {
        "asins": {
          "description": "Amazon ASINs to query. Provide 1 to 20 10-character alphanumeric values; the MCP gateway trims values, uppercases ASINs, and collapses duplicates before calling the upstream API.",
          "type": "array",
          "items": {
            "type": "string",
            "description": "Amazon ASINs to query. Provide 1 to 20 10-character alphanumeric values; the MCP gateway trims values, uppercases ASINs, and collapses duplicates before calling the upstream API."
          }
        },
        "countryCode": {
          "type": "string",
          "description": "Optional two-letter marketplace country code such as US, JP, or DE. When omitted, the server uses jiimore.interface-layer.default-country-code. Configured supported-country-codes may reject otherwise valid two-letter codes."
        }
      },
      "required": [
        "asins"
      ],
      "description": "Request containing 1 to 20 Amazon ASINs and an optional marketplace country code. countryCode is optional, must be a two-letter marketplace code, defaults to the server configured country when omitted, and must pass the server supported-country-codes whitelist. Unknown request fields are rejected."
    }
  },
  "required": [
    "request"
  ]
}
```

### 工具注解和安全声明

```json
{
  "annotations": {
    "title": "ASIN Details",
    "readOnlyHint": true,
    "destructiveHint": false,
    "idempotentHint": false,
    "openWorldHint": true
  },
  "_meta": {
    "securitySchemes": [
      {
        "type": "oauth2",
        "scopes": [
          "mcp:full"
        ]
      }
    ]
  }
}
```

## jiimore_find_asins_batch

**标题：** Find ASINs Batch

**说明：** Fetch one product summary for each of up to 5 requested ASINs by calling interface-layer asin/pageByAsin with page=1 and pageSize=1 per ASIN. Runs bounded parallel upstream calls, returns partial-failure warnings when some ASINs fail, and fails the tool when all upstream calls fail. Pagination parameters are accepted by the request schema but ignored; list price fields may be incomplete. Upstream Opportunity Explorer calls use week-grade marketplace data.

### 输入参数

```json
{
  "type": "object",
  "properties": {
    "request": {
      "type": "object",
      "properties": {
        "asins": {
          "description": "Amazon ASINs to query. Provide 1 to 5 10-character alphanumeric values; each normalized ASIN triggers one bounded upstream call, and duplicate values are collapsed before execution.",
          "type": "array",
          "items": {
            "type": "string",
            "description": "Amazon ASINs to query. Provide 1 to 5 10-character alphanumeric values; each normalized ASIN triggers one bounded upstream call, and duplicate values are collapsed before execution."
          }
        },
        "countryCode": {
          "type": "string",
          "description": "Optional two-letter marketplace country code such as US, JP, or DE. When omitted, the server uses jiimore.interface-layer.default-country-code. Configured supported-country-codes may reject otherwise valid two-letter codes."
        },
        "page": {
          "type": "integer",
          "format": "int32",
          "description": "Optional one-based page number for paged tools. Defaults to 1 when omitted; ignored by tools that explicitly return one summary per requested input."
        },
        "pageSize": {
          "type": "integer",
          "format": "int32",
          "description": "Optional page size from 1 to 50. Defaults to 20 when omitted; MCP may truncate oversized upstream pages and set response.truncated/warnings."
        }
      },
      "required": [
        "asins"
      ],
      "description": "Request containing 1 to 5 Amazon ASINs and an optional marketplace country code. Pagination controls are accepted but ignored; one summary is returned per normalized ASIN. countryCode is optional, must be a two-letter marketplace code, defaults to the server configured country when omitted, and must pass the server supported-country-codes whitelist. Unknown request fields are rejected."
    }
  },
  "required": [
    "request"
  ]
}
```

### 工具注解和安全声明

```json
{
  "annotations": {
    "title": "Find ASINs Batch",
    "readOnlyHint": true,
    "destructiveHint": false,
    "idempotentHint": false,
    "openWorldHint": true
  },
  "_meta": {
    "securitySchemes": [
      {
        "type": "oauth2",
        "scopes": [
          "mcp:full"
        ]
      }
    ]
  }
}
```

## jiimore_find_asins_by_keyword

**标题：** Find ASINs by Keyword

**说明：** Find Amazon products through interface-layer asin/pageByKeyword. Results are based on keyword-driven category discovery and may include behaviorally related products, not only exact keyword matches. Returns product summary, weekly click/conversion metrics, and marketplace ranking signals; list price fields may be incomplete. The upstream interface first resolves matching categories; when no category is found the tool returns an empty page. Upstream Opportunity Explorer calls use week-grade marketplace data.

### 输入参数

```json
{
  "type": "object",
  "properties": {
    "request": {
      "type": "object",
      "properties": {
        "countryCode": {
          "type": "string",
          "description": "Optional two-letter marketplace country code such as US, JP, or DE. When omitted, the server uses jiimore.interface-layer.default-country-code. Configured supported-country-codes may reject otherwise valid two-letter codes."
        },
        "keyword": {
          "type": "string",
          "description": "Search keyword or phrase to query. Provide 1 to 200 non-blank characters; leading and trailing whitespace is trimmed."
        },
        "page": {
          "type": "integer",
          "format": "int32",
          "description": "Optional one-based page number for paged tools. Defaults to 1 when omitted; ignored by tools that explicitly return one summary per requested input."
        },
        "pageSize": {
          "type": "integer",
          "format": "int32",
          "description": "Optional page size from 1 to 50. Defaults to 20 when omitted; MCP may truncate oversized upstream pages and set response.truncated/warnings."
        }
      },
      "required": [
        "keyword"
      ],
      "description": "Request containing a non-blank keyword up to 200 characters, optional marketplace country code, and pagination controls for product search. countryCode is optional, must be a two-letter marketplace code, defaults to the server configured country when omitted, and must pass the server supported-country-codes whitelist. Pagination is one-based: page defaults to 1, pageSize defaults to 20, and pageSize accepts 1 to 50. Oversized upstream result pages may be truncated by MCP with response warnings. Unknown request fields are rejected."
    }
  },
  "required": [
    "request"
  ]
}
```

### 工具注解和安全声明

```json
{
  "annotations": {
    "title": "Find ASINs by Keyword",
    "readOnlyHint": true,
    "destructiveHint": false,
    "idempotentHint": false,
    "openWorldHint": true
  },
  "_meta": {
    "securitySchemes": [
      {
        "type": "oauth2",
        "scopes": [
          "mcp:full"
        ]
      }
    ]
  }
}
```

## jiimore_find_same_niche_asins

**标题：** Find Same-Niche ASINs

**说明：** Find products from the same niches as one ASIN through interface-layer asin/sameNicheAsin. The upstream interface first fetches ASIN detail to discover niche IDs; if the ASIN has no detail or no niches, the tool returns an empty page. Returns similar product summaries and weekly marketplace metrics; list price fields may be incomplete. Upstream Opportunity Explorer calls use week-grade marketplace data.

### 输入参数

```json
{
  "type": "object",
  "properties": {
    "request": {
      "type": "object",
      "properties": {
        "asin": {
          "type": "string",
          "description": "Amazon ASIN to query. Provide one 10-character alphanumeric value; the MCP gateway trims and uppercases it."
        },
        "countryCode": {
          "type": "string",
          "description": "Optional two-letter marketplace country code such as US, JP, or DE. When omitted, the server uses jiimore.interface-layer.default-country-code. Configured supported-country-codes may reject otherwise valid two-letter codes."
        },
        "page": {
          "type": "integer",
          "format": "int32",
          "description": "Optional one-based page number for paged tools. Defaults to 1 when omitted; ignored by tools that explicitly return one summary per requested input."
        },
        "pageSize": {
          "type": "integer",
          "format": "int32",
          "description": "Optional page size from 1 to 50. Defaults to 20 when omitted; MCP may truncate oversized upstream pages and set response.truncated/warnings."
        }
      },
      "required": [
        "asin"
      ],
      "description": "Request containing one 10-character Amazon ASIN, optional marketplace country code, and pagination controls for same-niche products. countryCode is optional, must be a two-letter marketplace code, defaults to the server configured country when omitted, and must pass the server supported-country-codes whitelist. Pagination is one-based: page defaults to 1, pageSize defaults to 20, and pageSize accepts 1 to 50. Oversized upstream result pages may be truncated by MCP with response warnings. Unknown request fields are rejected."
    }
  },
  "required": [
    "request"
  ]
}
```

### 工具注解和安全声明

```json
{
  "annotations": {
    "title": "Find Same-Niche ASINs",
    "readOnlyHint": true,
    "destructiveHint": false,
    "idempotentHint": false,
    "openWorldHint": true
  },
  "_meta": {
    "securitySchemes": [
      {
        "type": "oauth2",
        "scopes": [
          "mcp:full"
        ]
      }
    ]
  }
}
```

## jiimore_get_keyword_rankings

**标题：** Get Keyword Rankings

**说明：** Retrieve ASIN reverse keyword ranking through interface-layer keyword/pageSearchKeyword. Returns ABA Flow keyword metrics, organic rank fields, first-page/top-16 ASIN counts, and flow scores for a selected 7-day or 30-day window. The upstream endpoint can parse comma-separated ASINs, but this MCP tool intentionally accepts only one ASIN per request. Upstream Opportunity Explorer calls use week-grade marketplace data.

### 输入参数

```json
{
  "type": "object",
  "properties": {
    "request": {
      "type": "object",
      "properties": {
        "asin": {
          "type": "string",
          "description": "Amazon ASIN to query. Provide one 10-character alphanumeric value; the MCP gateway trims and uppercases it."
        },
        "countryCode": {
          "type": "string",
          "description": "Optional two-letter marketplace country code such as US, JP, or DE. When omitted, the server uses jiimore.interface-layer.default-country-code. Configured supported-country-codes may reject otherwise valid two-letter codes."
        },
        "days": {
          "type": "integer",
          "format": "int32",
          "description": "Lookback window in days. Supported values are 7 and 30, matching the upstream SearchKeywordReq.Days enum; defaults to 7 when omitted."
        },
        "page": {
          "type": "integer",
          "format": "int32",
          "description": "Optional one-based page number for paged tools. Defaults to 1 when omitted; ignored by tools that explicitly return one summary per requested input."
        },
        "pageSize": {
          "type": "integer",
          "format": "int32",
          "description": "Optional page size from 1 to 50. Defaults to 20 when omitted; MCP may truncate oversized upstream pages and set response.truncated/warnings."
        }
      },
      "required": [
        "asin"
      ],
      "description": "Request containing one 10-character Amazon ASIN, a 7-day or 30-day lookback window, optional marketplace country code, and pagination controls. countryCode is optional, must be a two-letter marketplace code, defaults to the server configured country when omitted, and must pass the server supported-country-codes whitelist. Pagination is one-based: page defaults to 1, pageSize defaults to 20, and pageSize accepts 1 to 50. Oversized upstream result pages may be truncated by MCP with response warnings. Unknown request fields are rejected."
    }
  },
  "required": [
    "request"
  ]
}
```

### 工具注解和安全声明

```json
{
  "annotations": {
    "title": "Get Keyword Rankings",
    "readOnlyHint": true,
    "destructiveHint": false,
    "idempotentHint": false,
    "openWorldHint": true
  },
  "_meta": {
    "securitySchemes": [
      {
        "type": "oauth2",
        "scopes": [
          "mcp:full"
        ]
      }
    ]
  }
}
```

## jiimore_search_keywords_batch

**标题：** Search Keywords Batch

**说明：** Find keyword metrics for multiple marketplace search terms through interface-layer keyword/pageByBatchKeyword. This is a batch keyword lookup using the upstream batch flag, not a comma-separated single keyword search. Returns related keyword demand, click, sales, conversion, ABA rank, and niche context metrics. Upstream Opportunity Explorer calls use week-grade marketplace data.

### 输入参数

```json
{
  "type": "object",
  "properties": {
    "request": {
      "type": "object",
      "properties": {
        "countryCode": {
          "type": "string",
          "description": "Optional two-letter marketplace country code such as US, JP, or DE. When omitted, the server uses jiimore.interface-layer.default-country-code. Configured supported-country-codes may reject otherwise valid two-letter codes."
        },
        "keywords": {
          "description": "Search keywords or phrases to query. Provide 1 to 20 non-blank values, each up to 200 characters; the MCP gateway trims values and collapses duplicates before calling the upstream API.",
          "type": "array",
          "items": {
            "type": "string",
            "description": "Search keywords or phrases to query. Provide 1 to 20 non-blank values, each up to 200 characters; the MCP gateway trims values and collapses duplicates before calling the upstream API."
          }
        },
        "page": {
          "type": "integer",
          "format": "int32",
          "description": "Optional one-based page number for paged tools. Defaults to 1 when omitted; ignored by tools that explicitly return one summary per requested input."
        },
        "pageSize": {
          "type": "integer",
          "format": "int32",
          "description": "Optional page size from 1 to 50. Defaults to 20 when omitted; MCP may truncate oversized upstream pages and set response.truncated/warnings."
        }
      },
      "required": [
        "keywords"
      ],
      "description": "Request containing 1 to 20 non-blank search keywords, each up to 200 characters, optional marketplace country code, and pagination controls. countryCode is optional, must be a two-letter marketplace code, defaults to the server configured country when omitted, and must pass the server supported-country-codes whitelist. Pagination is one-based: page defaults to 1, pageSize defaults to 20, and pageSize accepts 1 to 50. Oversized upstream result pages may be truncated by MCP with response warnings. Unknown request fields are rejected."
    }
  },
  "required": [
    "request"
  ]
}
```

### 工具注解和安全声明

```json
{
  "annotations": {
    "title": "Search Keywords Batch",
    "readOnlyHint": true,
    "destructiveHint": false,
    "idempotentHint": false,
    "openWorldHint": true
  },
  "_meta": {
    "securitySchemes": [
      {
        "type": "oauth2",
        "scopes": [
          "mcp:full"
        ]
      }
    ]
  }
}
```

## jiimore_search_keywords_by_asin

**标题：** Search Keywords by ASIN

**说明：** Find search terms through interface-layer keyword/pageByAsin. The upstream interface resolves categories from the ASIN, then returns related keyword demand, click, sales, conversion, ABA rank, and niche context metrics. The upstream interface first resolves matching categories; when no category is found the tool returns an empty page. Upstream Opportunity Explorer calls use week-grade marketplace data.

### 输入参数

```json
{
  "type": "object",
  "properties": {
    "request": {
      "type": "object",
      "properties": {
        "asin": {
          "type": "string",
          "description": "Amazon ASIN to query. Provide one 10-character alphanumeric value; the MCP gateway trims and uppercases it."
        },
        "countryCode": {
          "type": "string",
          "description": "Optional two-letter marketplace country code such as US, JP, or DE. When omitted, the server uses jiimore.interface-layer.default-country-code. Configured supported-country-codes may reject otherwise valid two-letter codes."
        },
        "page": {
          "type": "integer",
          "format": "int32",
          "description": "Optional one-based page number for paged tools. Defaults to 1 when omitted; ignored by tools that explicitly return one summary per requested input."
        },
        "pageSize": {
          "type": "integer",
          "format": "int32",
          "description": "Optional page size from 1 to 50. Defaults to 20 when omitted; MCP may truncate oversized upstream pages and set response.truncated/warnings."
        }
      },
      "required": [
        "asin"
      ],
      "description": "Request containing one 10-character Amazon ASIN, optional marketplace country code, and pagination controls for keyword lookup. countryCode is optional, must be a two-letter marketplace code, defaults to the server configured country when omitted, and must pass the server supported-country-codes whitelist. Pagination is one-based: page defaults to 1, pageSize defaults to 20, and pageSize accepts 1 to 50. Oversized upstream result pages may be truncated by MCP with response warnings. Unknown request fields are rejected."
    }
  },
  "required": [
    "request"
  ]
}
```

### 工具注解和安全声明

```json
{
  "annotations": {
    "title": "Search Keywords by ASIN",
    "readOnlyHint": true,
    "destructiveHint": false,
    "idempotentHint": false,
    "openWorldHint": true
  },
  "_meta": {
    "securitySchemes": [
      {
        "type": "oauth2",
        "scopes": [
          "mcp:full"
        ]
      }
    ]
  }
}
```

## jiimore_search_keywords_by_keyword

**标题：** Expand Keywords by Keyword

**说明：** Expand a seed keyword through interface-layer keyword/pageByKeyword. The upstream interface resolves categories from the seed keyword, then returns related search terms with demand, click, sales, conversion, ABA rank, and niche context metrics. The upstream interface first resolves matching categories; when no category is found the tool returns an empty page. Upstream Opportunity Explorer calls use week-grade marketplace data.

### 输入参数

```json
{
  "type": "object",
  "properties": {
    "request": {
      "type": "object",
      "properties": {
        "countryCode": {
          "type": "string",
          "description": "Optional two-letter marketplace country code such as US, JP, or DE. When omitted, the server uses jiimore.interface-layer.default-country-code. Configured supported-country-codes may reject otherwise valid two-letter codes."
        },
        "keyword": {
          "type": "string",
          "description": "Search keyword or phrase to query. Provide 1 to 200 non-blank characters; leading and trailing whitespace is trimmed."
        },
        "page": {
          "type": "integer",
          "format": "int32",
          "description": "Optional one-based page number for paged tools. Defaults to 1 when omitted; ignored by tools that explicitly return one summary per requested input."
        },
        "pageSize": {
          "type": "integer",
          "format": "int32",
          "description": "Optional page size from 1 to 50. Defaults to 20 when omitted; MCP may truncate oversized upstream pages and set response.truncated/warnings."
        }
      },
      "required": [
        "keyword"
      ],
      "description": "Request containing a non-blank seed keyword up to 200 characters, optional marketplace country code, and pagination controls for keyword expansion. countryCode is optional, must be a two-letter marketplace code, defaults to the server configured country when omitted, and must pass the server supported-country-codes whitelist. Pagination is one-based: page defaults to 1, pageSize defaults to 20, and pageSize accepts 1 to 50. Oversized upstream result pages may be truncated by MCP with response warnings. Unknown request fields are rejected."
    }
  },
  "required": [
    "request"
  ]
}
```

### 工具注解和安全声明

```json
{
  "annotations": {
    "title": "Expand Keywords by Keyword",
    "readOnlyHint": true,
    "destructiveHint": false,
    "idempotentHint": false,
    "openWorldHint": true
  },
  "_meta": {
    "securitySchemes": [
      {
        "type": "oauth2",
        "scopes": [
          "mcp:full"
        ]
      }
    ]
  }
}
```

## jiimore_find_niches_by_asin

**标题：** Find Niches by ASIN

**说明：** Find marketplace niches through interface-layer niche/pageByAsin. The upstream interface resolves categories from the ASIN, then returns niche demand, conversion, product count, brand count, competition, price, and trend metrics. The upstream interface first resolves matching categories; when no category is found the tool returns an empty page. Upstream Opportunity Explorer calls use week-grade marketplace data.

### 输入参数

```json
{
  "type": "object",
  "properties": {
    "request": {
      "type": "object",
      "properties": {
        "asin": {
          "type": "string",
          "description": "Amazon ASIN to query. Provide one 10-character alphanumeric value; the MCP gateway trims and uppercases it."
        },
        "countryCode": {
          "type": "string",
          "description": "Optional two-letter marketplace country code such as US, JP, or DE. When omitted, the server uses jiimore.interface-layer.default-country-code. Configured supported-country-codes may reject otherwise valid two-letter codes."
        },
        "page": {
          "type": "integer",
          "format": "int32",
          "description": "Optional one-based page number for paged tools. Defaults to 1 when omitted; ignored by tools that explicitly return one summary per requested input."
        },
        "pageSize": {
          "type": "integer",
          "format": "int32",
          "description": "Optional page size from 1 to 50. Defaults to 20 when omitted; MCP may truncate oversized upstream pages and set response.truncated/warnings."
        }
      },
      "required": [
        "asin"
      ],
      "description": "Request containing one 10-character Amazon ASIN, optional marketplace country code, and pagination controls for niche search. countryCode is optional, must be a two-letter marketplace code, defaults to the server configured country when omitted, and must pass the server supported-country-codes whitelist. Pagination is one-based: page defaults to 1, pageSize defaults to 20, and pageSize accepts 1 to 50. Oversized upstream result pages may be truncated by MCP with response warnings. Unknown request fields are rejected."
    }
  },
  "required": [
    "request"
  ]
}
```

### 工具注解和安全声明

```json
{
  "annotations": {
    "title": "Find Niches by ASIN",
    "readOnlyHint": true,
    "destructiveHint": false,
    "idempotentHint": false,
    "openWorldHint": true
  },
  "_meta": {
    "securitySchemes": [
      {
        "type": "oauth2",
        "scopes": [
          "mcp:full"
        ]
      }
    ]
  }
}
```

## jiimore_find_niches_by_keyword

**标题：** Find Niches by Keyword

**说明：** Find marketplace niches through interface-layer niche/pageByKeyword. The upstream interface resolves categories from the keyword, then returns niche demand, conversion, product count, brand count, competition, price, and trend metrics. The upstream interface first resolves matching categories; when no category is found the tool returns an empty page. Upstream Opportunity Explorer calls use week-grade marketplace data.

### 输入参数

```json
{
  "type": "object",
  "properties": {
    "request": {
      "type": "object",
      "properties": {
        "countryCode": {
          "type": "string",
          "description": "Optional two-letter marketplace country code such as US, JP, or DE. When omitted, the server uses jiimore.interface-layer.default-country-code. Configured supported-country-codes may reject otherwise valid two-letter codes."
        },
        "keyword": {
          "type": "string",
          "description": "Search keyword or phrase to query. Provide 1 to 200 non-blank characters; leading and trailing whitespace is trimmed."
        },
        "page": {
          "type": "integer",
          "format": "int32",
          "description": "Optional one-based page number for paged tools. Defaults to 1 when omitted; ignored by tools that explicitly return one summary per requested input."
        },
        "pageSize": {
          "type": "integer",
          "format": "int32",
          "description": "Optional page size from 1 to 50. Defaults to 20 when omitted; MCP may truncate oversized upstream pages and set response.truncated/warnings."
        }
      },
      "required": [
        "keyword"
      ],
      "description": "Request containing a non-blank keyword up to 200 characters, optional marketplace country code, and pagination controls for niche search. countryCode is optional, must be a two-letter marketplace code, defaults to the server configured country when omitted, and must pass the server supported-country-codes whitelist. Pagination is one-based: page defaults to 1, pageSize defaults to 20, and pageSize accepts 1 to 50. Oversized upstream result pages may be truncated by MCP with response warnings. Unknown request fields are rejected."
    }
  },
  "required": [
    "request"
  ]
}
```

### 工具注解和安全声明

```json
{
  "annotations": {
    "title": "Find Niches by Keyword",
    "readOnlyHint": true,
    "destructiveHint": false,
    "idempotentHint": false,
    "openWorldHint": true
  },
  "_meta": {
    "securitySchemes": [
      {
        "type": "oauth2",
        "scopes": [
          "mcp:full"
        ]
      }
    ]
  }
}
```

## jiimore_get_niche_asins

**标题：** Get Niche ASINs

**说明：** Retrieve products in a niche through interface-layer niche/asinInNiche. MCP requests the asinInNiche property with per-property pagination and returns product summaries plus 90-day niche click share/count metrics. Upstream Opportunity Explorer calls use week-grade marketplace data.

### 输入参数

```json
{
  "type": "object",
  "properties": {
    "request": {
      "type": "object",
      "properties": {
        "countryCode": {
          "type": "string",
          "description": "Optional two-letter marketplace country code such as US, JP, or DE. When omitted, the server uses jiimore.interface-layer.default-country-code. Configured supported-country-codes may reject otherwise valid two-letter codes."
        },
        "nicheId": {
          "type": "string",
          "description": "Marketplace niche identifier to query. Provide a non-blank value up to 128 characters."
        },
        "page": {
          "type": "integer",
          "format": "int32",
          "description": "Optional one-based page number for paged tools. Defaults to 1 when omitted; ignored by tools that explicitly return one summary per requested input."
        },
        "pageSize": {
          "type": "integer",
          "format": "int32",
          "description": "Optional page size from 1 to 50. Defaults to 20 when omitted; MCP may truncate oversized upstream pages and set response.truncated/warnings."
        }
      },
      "required": [
        "nicheId"
      ],
      "description": "Request containing a non-blank niche identifier up to 128 characters, optional marketplace country code, and pagination controls for niche products. countryCode is optional, must be a two-letter marketplace code, defaults to the server configured country when omitted, and must pass the server supported-country-codes whitelist. Pagination is one-based: page defaults to 1, pageSize defaults to 20, and pageSize accepts 1 to 50. Oversized upstream result pages may be truncated by MCP with response warnings. Unknown request fields are rejected."
    }
  },
  "required": [
    "request"
  ]
}
```

### 工具注解和安全声明

```json
{
  "annotations": {
    "title": "Get Niche ASINs",
    "readOnlyHint": true,
    "destructiveHint": false,
    "idempotentHint": false,
    "openWorldHint": true
  },
  "_meta": {
    "securitySchemes": [
      {
        "type": "oauth2",
        "scopes": [
          "mcp:full"
        ]
      }
    ]
  }
}
```

## jiimore_get_niche_details

**标题：** Get Niche Details

**说明：** Retrieve niche details through interface-layer niche/nicheInfo. MCP requests baseInfo, PDR review insights, and nichePotential only; niche products and niche keywords are exposed by separate tools. Upstream Opportunity Explorer calls use week-grade marketplace data.

### 输入参数

```json
{
  "type": "object",
  "properties": {
    "request": {
      "type": "object",
      "properties": {
        "countryCode": {
          "type": "string",
          "description": "Optional two-letter marketplace country code such as US, JP, or DE. When omitted, the server uses jiimore.interface-layer.default-country-code. Configured supported-country-codes may reject otherwise valid two-letter codes."
        },
        "nicheId": {
          "type": "string",
          "description": "Marketplace niche identifier to query. Provide a non-blank value up to 128 characters."
        }
      },
      "required": [
        "nicheId"
      ],
      "description": "Request containing a non-blank niche identifier up to 128 characters and an optional marketplace country code. countryCode is optional, must be a two-letter marketplace code, defaults to the server configured country when omitted, and must pass the server supported-country-codes whitelist. Unknown request fields are rejected."
    }
  },
  "required": [
    "request"
  ]
}
```

### 工具注解和安全声明

```json
{
  "annotations": {
    "title": "Get Niche Details",
    "readOnlyHint": true,
    "destructiveHint": false,
    "idempotentHint": false,
    "openWorldHint": true
  },
  "_meta": {
    "securitySchemes": [
      {
        "type": "oauth2",
        "scopes": [
          "mcp:full"
        ]
      }
    ]
  }
}
```

## jiimore_get_niche_keywords

**标题：** Get Niche Keywords

**说明：** Retrieve keywords in a niche through interface-layer niche/keywordInNiche. MCP requests the keywordInNiche property with per-property pagination and returns keyword metrics plus 90-day niche click/share/search metrics. Upstream Opportunity Explorer calls use week-grade marketplace data.

### 输入参数

```json
{
  "type": "object",
  "properties": {
    "request": {
      "type": "object",
      "properties": {
        "countryCode": {
          "type": "string",
          "description": "Optional two-letter marketplace country code such as US, JP, or DE. When omitted, the server uses jiimore.interface-layer.default-country-code. Configured supported-country-codes may reject otherwise valid two-letter codes."
        },
        "nicheId": {
          "type": "string",
          "description": "Marketplace niche identifier to query. Provide a non-blank value up to 128 characters."
        },
        "page": {
          "type": "integer",
          "format": "int32",
          "description": "Optional one-based page number for paged tools. Defaults to 1 when omitted; ignored by tools that explicitly return one summary per requested input."
        },
        "pageSize": {
          "type": "integer",
          "format": "int32",
          "description": "Optional page size from 1 to 50. Defaults to 20 when omitted; MCP may truncate oversized upstream pages and set response.truncated/warnings."
        }
      },
      "required": [
        "nicheId"
      ],
      "description": "Request containing a non-blank niche identifier up to 128 characters, optional marketplace country code, and pagination controls for niche keywords. countryCode is optional, must be a two-letter marketplace code, defaults to the server configured country when omitted, and must pass the server supported-country-codes whitelist. Pagination is one-based: page defaults to 1, pageSize defaults to 20, and pageSize accepts 1 to 50. Oversized upstream result pages may be truncated by MCP with response warnings. Unknown request fields are rejected."
    }
  },
  "required": [
    "request"
  ]
}
```

### 工具注解和安全声明

```json
{
  "annotations": {
    "title": "Get Niche Keywords",
    "readOnlyHint": true,
    "destructiveHint": false,
    "idempotentHint": false,
    "openWorldHint": true
  },
  "_meta": {
    "securitySchemes": [
      {
        "type": "oauth2",
        "scopes": [
          "mcp:full"
        ]
      }
    ]
  }
}
```

## jiimore_expand_aba_keywords_by_keyword

**标题：** Expand ABA Keywords by Keyword

**说明：** Expand seed keywords through interface-layer aba/abaKeywordSearchKeyword. Accepts one or more keywords and returns related ABA keyword demand, click, sales, conversion, ABA rank, relation grade, competition, top ASINs, and advertising estimate metrics. Upstream Opportunity Explorer calls use week-grade marketplace data.

### 输入参数

```json
{
  "type": "object",
  "properties": {
    "request": {
      "type": "object",
      "properties": {
        "countryCode": {
          "type": "string",
          "description": "Optional two-letter marketplace country code such as US, JP, or DE. When omitted, the server uses jiimore.interface-layer.default-country-code. Configured supported-country-codes may reject otherwise valid two-letter codes."
        },
        "keywords": {
          "description": "Search keywords or phrases to query. Provide 1 to 20 non-blank values, each up to 200 characters; the MCP gateway trims values and collapses duplicates before calling the upstream API.",
          "type": "array",
          "items": {
            "type": "string",
            "description": "Search keywords or phrases to query. Provide 1 to 20 non-blank values, each up to 200 characters; the MCP gateway trims values and collapses duplicates before calling the upstream API."
          }
        },
        "page": {
          "type": "integer",
          "format": "int32",
          "description": "Optional one-based page number for paged tools. Defaults to 1 when omitted; ignored by tools that explicitly return one summary per requested input."
        },
        "pageSize": {
          "type": "integer",
          "format": "int32",
          "description": "Optional page size from 1 to 50. Defaults to 20 when omitted; MCP may truncate oversized upstream pages and set response.truncated/warnings."
        }
      },
      "required": [
        "keywords"
      ],
      "description": "Request containing 1 to 20 non-blank seed keywords, each up to 200 characters, optional marketplace country code, and pagination controls for ABA keyword expansion. countryCode is optional, must be a two-letter marketplace code, defaults to the server configured country when omitted, and must pass the server supported-country-codes whitelist. Pagination is one-based: page defaults to 1, pageSize defaults to 20, and pageSize accepts 1 to 50. Oversized upstream result pages may be truncated by MCP with response warnings. Unknown request fields are rejected."
    }
  },
  "required": [
    "request"
  ]
}
```

### 工具注解和安全声明

```json
{
  "annotations": {
    "title": "Expand ABA Keywords by Keyword",
    "readOnlyHint": true,
    "destructiveHint": false,
    "idempotentHint": false,
    "openWorldHint": true
  },
  "_meta": {
    "securitySchemes": [
      {
        "type": "oauth2",
        "scopes": [
          "mcp:full"
        ]
      }
    ]
  }
}
```

## jiimore_find_aba_asins_by_keyword

**标题：** Find ABA ASINs by Keyword

**说明：** Find ABA products through interface-layer aba/abaAsinByKeyword. Accepts one or more keywords and returns product summaries with similarity/relevance metrics and weekly marketplace signals. Upstream Opportunity Explorer calls use week-grade marketplace data.

### 输入参数

```json
{
  "type": "object",
  "properties": {
    "request": {
      "type": "object",
      "properties": {
        "countryCode": {
          "type": "string",
          "description": "Optional two-letter marketplace country code such as US, JP, or DE. When omitted, the server uses jiimore.interface-layer.default-country-code. Configured supported-country-codes may reject otherwise valid two-letter codes."
        },
        "keywords": {
          "description": "Search keywords or phrases to query. Provide 1 to 20 non-blank values, each up to 200 characters; the MCP gateway trims values and collapses duplicates before calling the upstream API.",
          "type": "array",
          "items": {
            "type": "string",
            "description": "Search keywords or phrases to query. Provide 1 to 20 non-blank values, each up to 200 characters; the MCP gateway trims values and collapses duplicates before calling the upstream API."
          }
        },
        "page": {
          "type": "integer",
          "format": "int32",
          "description": "Optional one-based page number for paged tools. Defaults to 1 when omitted; ignored by tools that explicitly return one summary per requested input."
        },
        "pageSize": {
          "type": "integer",
          "format": "int32",
          "description": "Optional page size from 1 to 50. Defaults to 20 when omitted; MCP may truncate oversized upstream pages and set response.truncated/warnings."
        }
      },
      "required": [
        "keywords"
      ],
      "description": "Request containing 1 to 20 non-blank search keywords, each up to 200 characters, optional marketplace country code, and pagination controls for ABA product search. countryCode is optional, must be a two-letter marketplace code, defaults to the server configured country when omitted, and must pass the server supported-country-codes whitelist. Pagination is one-based: page defaults to 1, pageSize defaults to 20, and pageSize accepts 1 to 50. Oversized upstream result pages may be truncated by MCP with response warnings. Unknown request fields are rejected."
    }
  },
  "required": [
    "request"
  ]
}
```

### 工具注解和安全声明

```json
{
  "annotations": {
    "title": "Find ABA ASINs by Keyword",
    "readOnlyHint": true,
    "destructiveHint": false,
    "idempotentHint": false,
    "openWorldHint": true
  },
  "_meta": {
    "securitySchemes": [
      {
        "type": "oauth2",
        "scopes": [
          "mcp:full"
        ]
      }
    ]
  }
}
```

## jiimore_find_similar_asins

**标题：** Find Similar ASINs

**说明：** Find similar products through interface-layer aba/abaSimilarityAsin. Accepts one or more ASINs, uses ABA weekly similarity data, and returns product summaries with similarity/relevance fields. List price fields may be incomplete. Upstream Opportunity Explorer calls use week-grade marketplace data.

### 输入参数

```json
{
  "type": "object",
  "properties": {
    "request": {
      "type": "object",
      "properties": {
        "asins": {
          "description": "Amazon ASINs to query. Provide 1 to 20 10-character alphanumeric values; the MCP gateway trims values, uppercases ASINs, and collapses duplicates before calling the upstream API.",
          "type": "array",
          "items": {
            "type": "string",
            "description": "Amazon ASINs to query. Provide 1 to 20 10-character alphanumeric values; the MCP gateway trims values, uppercases ASINs, and collapses duplicates before calling the upstream API."
          }
        },
        "countryCode": {
          "type": "string",
          "description": "Optional two-letter marketplace country code such as US, JP, or DE. When omitted, the server uses jiimore.interface-layer.default-country-code. Configured supported-country-codes may reject otherwise valid two-letter codes."
        },
        "page": {
          "type": "integer",
          "format": "int32",
          "description": "Optional one-based page number for paged tools. Defaults to 1 when omitted; ignored by tools that explicitly return one summary per requested input."
        },
        "pageSize": {
          "type": "integer",
          "format": "int32",
          "description": "Optional page size from 1 to 50. Defaults to 20 when omitted; MCP may truncate oversized upstream pages and set response.truncated/warnings."
        }
      },
      "required": [
        "asins"
      ],
      "description": "Request containing 1 to 20 Amazon ASINs, optional marketplace country code, and pagination controls for similar products. countryCode is optional, must be a two-letter marketplace code, defaults to the server configured country when omitted, and must pass the server supported-country-codes whitelist. Pagination is one-based: page defaults to 1, pageSize defaults to 20, and pageSize accepts 1 to 50. Oversized upstream result pages may be truncated by MCP with response warnings. Unknown request fields are rejected."
    }
  },
  "required": [
    "request"
  ]
}
```

### 工具注解和安全声明

```json
{
  "annotations": {
    "title": "Find Similar ASINs",
    "readOnlyHint": true,
    "destructiveHint": false,
    "idempotentHint": false,
    "openWorldHint": true
  },
  "_meta": {
    "securitySchemes": [
      {
        "type": "oauth2",
        "scopes": [
          "mcp:full"
        ]
      }
    ]
  }
}
```

## jiimore_search_aba_keywords

**标题：** Search ABA Keywords

**说明：** Find ABA keywords through interface-layer aba/abaKeywordSearch. Accepts one or more ASINs and returns keyword demand, click, sales, conversion, ABA rank, relation grade, competition, top ASINs, and advertising estimate metrics. Upstream Opportunity Explorer calls use week-grade marketplace data.

### 输入参数

```json
{
  "type": "object",
  "properties": {
    "request": {
      "type": "object",
      "properties": {
        "asins": {
          "description": "Amazon ASINs to query. Provide 1 to 20 10-character alphanumeric values; the MCP gateway trims values, uppercases ASINs, and collapses duplicates before calling the upstream API.",
          "type": "array",
          "items": {
            "type": "string",
            "description": "Amazon ASINs to query. Provide 1 to 20 10-character alphanumeric values; the MCP gateway trims values, uppercases ASINs, and collapses duplicates before calling the upstream API."
          }
        },
        "countryCode": {
          "type": "string",
          "description": "Optional two-letter marketplace country code such as US, JP, or DE. When omitted, the server uses jiimore.interface-layer.default-country-code. Configured supported-country-codes may reject otherwise valid two-letter codes."
        },
        "page": {
          "type": "integer",
          "format": "int32",
          "description": "Optional one-based page number for paged tools. Defaults to 1 when omitted; ignored by tools that explicitly return one summary per requested input."
        },
        "pageSize": {
          "type": "integer",
          "format": "int32",
          "description": "Optional page size from 1 to 50. Defaults to 20 when omitted; MCP may truncate oversized upstream pages and set response.truncated/warnings."
        }
      },
      "required": [
        "asins"
      ],
      "description": "Request containing 1 to 20 Amazon ASINs, optional marketplace country code, and pagination controls for ABA keyword search. countryCode is optional, must be a two-letter marketplace code, defaults to the server configured country when omitted, and must pass the server supported-country-codes whitelist. Pagination is one-based: page defaults to 1, pageSize defaults to 20, and pageSize accepts 1 to 50. Oversized upstream result pages may be truncated by MCP with response warnings. Unknown request fields are rejected."
    }
  },
  "required": [
    "request"
  ]
}
```

### 工具注解和安全声明

```json
{
  "annotations": {
    "title": "Search ABA Keywords",
    "readOnlyHint": true,
    "destructiveHint": false,
    "idempotentHint": false,
    "openWorldHint": true
  },
  "_meta": {
    "securitySchemes": [
      {
        "type": "oauth2",
        "scopes": [
          "mcp:full"
        ]
      }
    ]
  }
}
```
