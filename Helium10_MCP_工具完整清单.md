# Helium 10 MCP 工具完整清单

共 116 个工具。下方是当前会话暴露的原始工具说明与参数定义；英文说明保留原文以避免遗漏约束。涉及修改广告、Listing、追踪或账户数据的工具需要按具体操作确认用户授权。

## 1. `add_untracked_product_keywords`

Helium 10 Keyword Tracker — START TRACKING keywords for a product. Adds a product to Keyword Tracker together with the keywords if it is not being tracked, or appends the keywords when it already is being tracked; you do not need to check which case applies first. Amazon only, one ASIN per call. Any valid ASIN is allowed, including competitors'. BEFORE CALLING: track_variations is required and has no default — if the user has not said whether they want the parent ASIN and all its variations tracked, or only the exact ASIN they gave, ASK THEM. Do not decide on their behalf. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_add_untracked_product_keywords(args: {
  // 10-character Amazon ASIN or Amazon product URL containing an ASIN; JSON key `asin_or_url`. Required and must not be blank. If the user has not provided an ASIN or product URL, ask them to supply it—do not infer, search for, or invent an ASIN.
  asin_or_url: string;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Keywords to start tracking for this product; JSON key `keywords`. No size limit. Duplicates within the list, and keywords already tracked on this product, are merged or skipped automatically — you do not need to de-duplicate first. Output from get_keywords_new_suggestion can be passed straight through.
  keywords: Array<string>;
  // Amazon marketplace short code (e.g. `US`, `UK`, `DE`, `JP`); JSON key `marketplace`. REQUIRED for this write operation and has no default — every tracked product belongs to exactly one marketplace, so it cannot be omitted or inferred. Must be one of the uppercase marketplace codes exposed in this schema; lowercase or mixed case is rejected.
  marketplace: "US" | "UK" | "CA" | "MX" | "DE" | "FR" | "IT" | "ES" | "IN" | "NL" | "JP" | "AU" | "AE" | "SA" | "BR";
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Whether to strip special characters from the keywords before tracking; JSON key `special_chars`. Defaults to true, matching the Helium 10 web 'add product' flow. Set to false only when the user explicitly wants punctuation preserved.
  special_chars?: boolean;
  // Which SHAPE to give the tracked product, chosen when tracking starts; JSON key `track_variations`. REQUIRED and has no default — if the user has not said which they want, ASK THEM before calling this tool; do not guess. true = the PARENT ASIN is tracked and its variations roll up into one entry. false = only the exact ASIN supplied is tracked. This is NOT an action that can be applied to a product that is already tracked: a tracked product's shape is fixed, and no tool (nor the Helium 10 web app) can change it afterwards. So do not send true to 'add variation tracking' to something already tracked — with an already-tracked parent that would only append your keywords to that parent and leave its shape untouched. To start tracking a specific variation of a product you already track, call this tool again with that variation's own child ASIN and `track_variations` false; it becomes a separate tracked product.
  track_variations: boolean;
}): Promise<CallToolResult<{
  // Write result when the call completes; interpret together with ``code``.
  data?: {
  // Submitted keywords discarded because nothing usable remained after cleaning. Report these back to the user verbatim.
  bad_phrases?: Array<string> | null;
  // Submitted keywords that were already tracked and were silently skipped. These consume no quota and keep their history.
  duplicates?: number | null;
  // Total keywords tracked on this product after the call.
  keywords_count?: number | null;
  // Downstream message. 'Keywords have not been changed.' means every submitted keyword was already tracked — that is a SUCCESS, not a failure.
  msg?: string | null;
  // True when this call started tracking a new product; false when keywords were appended to a product that was already tracked.
  product_created?: boolean | null;
  // Number of keywords newly tracked by this call.
  saved?: number | null;
  // Keywords that can still be added in the rolling 24-hour window.
  today_left?: number | null;
  // Keywords that can still be added against the total limit.
  total_left?: number | null;
  // Account-level Keyword Tracker keyword limit for the platform.
  total_limit?: number | null;
  // The ASIN actually tracked. When track_variations was true and a child ASIN was supplied, this is the PARENT ASIN and differs from the requested one — always report it to the user.
  tracked_asin?: string | null;
} | null;
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
}>>; };
```

## 2. `analyze_keywords`

Helium 10 Keyword Research (formerly Cerebro). Batch enrichment for an explicit list of 1–200 keywords: one row per keyword with search volume, CPC, ABA top-3 click/conversion share, and H10 metrics (CPR, IQ score, title density, competing products). Use when the user already has a candidate-keyword set to score, rank, structure into ad groups, or cross-compare. Not for: deriving new terms from one seed (use get_keywords_by_keyword); reverse search on an ASIN (use get_keywords_by_asin). Note: output order does NOT match input order — match results by the phrase field. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_analyze_keywords(args: {
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Explicit list of candidate phrases. Max 200 per call — split larger lists.
  keywords: Array<string>;
  // Marketplace code: US, UK, DE, JP, etc.
  marketplace: "US" | "CA" | "MX" | "DE" | "ES" | "IT" | "FR" | "UK" | "IN" | "NL" | "AU" | "JP" | "AE" | "BR" | "SA";
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{
  // Batch phrase scoring payload when the call succeeds. May be omitted or empty on failure—check for an ``error`` payload.
  data: {
  // One enriched row per phrase you submitted (same metric family as single-seed expansion, applied to your candidate list).
  keywords: Array<{
  // ABA Total Click Share — Keyword metrics, including click & conversion rates for the top 3 ASINs.
  aba_click_share?: number | null;
  // ABA Total Conv. Share — Gain valuable insights into the top 3 clicked products for each keyword;
  aba_conv_share?: number | null;
  // Amazon Choice — Badge / label context for the phrase (see Amazon Choice filter semantics in the Cerebro UI).
  amazon_choice?: string | null;
  // Competing Products — Total number of products returned in a customer search using this keyword/phrase.
  competing_products?: number;
  // CPR — Estimated number of units that need to be sold over 8 days, resulting from a search for this keyword in order for the product to rank on top half of page one for this keyword.
  cpr?: number;
  // Cerebro IQ Score — Score based on ratio of estimated search volume vs. number of competing products: a high score means a relatively high number of searches compared to the number of competitors; a low score means the opposite.
  iq_score?: number;
  // Keyword Sales — Estimated number of weekly sales (in number of units) attributed to each keyword
  keyword_sales_weekly?: number | null;
  // Keyword Phrase — Keyword or phrase used by customers in search for a product.
  phrase?: string;
  // Internal phrase id linking this row to Helium 10 phrase metadata when present.
  phrase_id?: number;
  // Search Volume — Estimated number of times this keyword is searched monthly on Amazon.
  search_volume?: number;
  // Search Volume Trend — Over the past 30 days (percentage change as a number).
  search_volume_trend_30d_pct?: number | null;
  // Sponsored ASINs — Number of ASINs detected running sponsored ads recently in search results associated with this keyword phrase.
  sponsored_asins_count?: number;
  // Suggested PPC Bid — Suggested bid and bid range are calculated from a group of winning bids for ads that are similar to yours. You can choose to use the suggested bid, or any bid in or outside of the bid range.
  suggested_bid_usd?: number | null;
  // Title Density — Number of page 1 products with the searched keyword in their title.
  title_density?: number;
}>;
};
} | {
  // Structured error response from io-api for MCP endpoints.
  error: {
  // MCP string error code.
  code: string;
  // Optional structured error details.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Optional human-readable remediation guidance (may be omitted).
  remediation?: string;
};
}>>; };
```

## 3. `analyze_product_images`

Helium 10 Listing Builder — extracts structured product attributes (product_name, brand_name, category, description, key_features, brand_voice, target_audience) from 1-5 product images. Live multimodal call, non-idempotent: re-running the same images may vary the attributes. Two input channels, mixable up to 5 combined — `image_urls` for catalog or public URLs, `image_refs` for LOCAL files, which come from request_image_upload_url rather than base64-ing a file into a data URL. Use to turn product photos into a structured product profile: copywriting attributes, brand voice, and target audience. Not for: pure OCR / screenshot text extraction (use a generic vision tool — this one is purpose-built for ecommerce attribute extraction). This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_analyze_product_images(args: {
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Mixable with image_urls; combined at most 5. Each element is an opaque upload reference returned by request_image_upload_url AFTER the file was uploaded (channel 1 curl PUT, or channel 2 browser upload page). This is the REQUIRED path for local files.
  image_refs?: Array<string> | null;
  // Mixable with image_refs; combined at most 5. Each element MUST match EXACTLY ONE of: (a) HTTPS URL ending in .jpg/.jpeg/.png whose host resolves to a public IP (http and internal / private / link-local hosts are rejected as SSRF); (b) data URL 'data:image/<jpg|jpeg|png>;base64,<BASE64>' with a valid JPEG (FF D8 FF) or PNG (89 50 4E 47 0D 0A 1A 0A) magic header. Bare base64 (no 'data:' prefix), .webp, .gif are REJECTED. Catalog images from `retrieve_listing_by_asin` arrive here via `images[].url`. Do NOT base64 a local file into a data URL — it wastes conversation tokens and may be rejected; use request_image_upload_url + image_refs instead.
  image_urls?: Array<string> | null;
  // REQUIRED. Marketplace short code (case-sensitive). Confirm the marketplace with the user once, before the first Listing Builder call of the session; every later Listing Builder tool reuses that same value — do NOT ask the user for it again once it is set. The output language for generated attributes is derived server-side from this value (e.g. 'JP' → Japanese). Supported values: US, CA, MX, DE, ES, IT, FR, UK, IN, NL, AU, JP, BE, BR.
  marketplace: "US" | "CA" | "MX" | "DE" | "ES" | "IT" | "FR" | "UK" | "IN" | "NL" | "AU" | "JP" | "BE" | "BR";
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{
  // Image-analysis payload: AI-extracted structured product attributes plus the generation record id to chain into generate_listing as parent_request_id.
  data: {
  // AI-extracted product attributes. Pass back as the product_info argument to generate_listing.
  analyzed_product?: {
  // Brand name identified in the images or inferred from packaging text.
  brand_name?: string | null;
  // Tone / voice descriptor for downstream copywriting (e.g. 'friendly', 'premium').
  brand_voice?: string | null;
  // Inferred product category for the listing.
  category?: string | null;
  // Generated long-form product description, optimized for downstream listing copy.
  description?: string | null;
  // Salient product features extracted from the images.
  key_features?: Array<string | null> | null;
  // AI-suggested product name derived from the analyzed product images.
  product_name?: string | null;
  // Intended target audience descriptor inferred from the images and category.
  target_audience?: string | null;
} | null;
  // Generation record id for this image-analysis call. Pass to generate_listing as parent_request_id.
  request_id?: number | null;
};
  // No error on success.
  error: null;
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
} | {
  // No data when the tool fails.
  data: null;
  // Tool execution error payload. Present when isError is true.
  error: {
  // Stable MCP tool error code.
  code: string;
  // Structured diagnostic details safe for the agent to inspect.
  details: { [key: string]: unknown; } | null;
  // Concise human-readable error message.
  message: string;
  // Actionable guidance the agent can use to correct and retry.
  remediation: string | null;
};
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
}>>; };
```

## 4. `check_asin_keyword_index`

Checks whether one Amazon ASIN is indexed for one or more keywords on a marketplace using Helium 10 Index Checker paths. Returns Traditional Index, Field-ASIN Index, Storefront Index when Seller ID is provided, and Cumulative as true or false on Completed rows (true if any applicable check is positive, else false if any applicable check is negative). If all applicable checks are null, treat the keyword as Check Failed and summarize it in additionalMessage instead of returning a result row. Without Seller ID, checks Traditional and Field-ASIN; with Seller ID, includes Storefront. Call for indexed / not indexed / keyword index / Index Checker questions—even without saying "index checker." Use for tracked Keyword Tracker ASINs (one call per ASIN) or ad-hoc ASIN + keywords + marketplace. Ask once if marketplace, ASIN, or keywords are missing; ask Seller ID only for Storefront. One ASIN per call; batch larger sets. Prefer unique keywords; duplicates are deduplicated and counted in the summary. Completed keywords return as rows; failures are summarized by count in additionalMessage. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_check_asin_keyword_index(args: {
  // ASIN to check. Exactly 10 alphanumeric characters; uppercase and lowercase accepted. Server normalizes to uppercase for processing and echo.
  asin: string;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Keywords to check for this ASIN. Prefer unique values. Duplicates are accepted and deduplicated server-side. One batch per call; Agent splits larger sets. Max 50.
  keywords: Array<string>;
  // Amazon marketplace code. Must be a supported uppercase enum value per platform MCP spec (e.g. US). Lowercase or mixed case is rejected.
  marketplace: "US" | "CA" | "MX" | "BR" | "DE" | "ES" | "FR" | "IT" | "UK" | "NL" | "TR" | "IN" | "AE" | "EG" | "SA" | "SE" | "PL" | "JP" | "AU" | "SG" | "CN" | "BE" | "ZA" | "IE";
  // Optional Seller ID. When provided, Storefront Index is included. Length must be 10-21 characters.
  seller_id?: string;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{
  // Index check results; null on hard failure.
  data: {
  // English summary of dedupe / completed / check-failed counts and failed keywords; used when isError=false for partial success (and whenever the summary is needed). Fixed template: 'Removed duplicates: {X}. Completed: {Y}. Check Failed: {Z}.' with ' Failed keywords: {keyword1}, {keyword2}.' appended only when Z > 0.
  additionalMessage?: string;
  // Echo of request ASIN.
  asin?: string;
  // Echo of request marketplace.
  marketplace?: string;
  results?: Array<{
  // Aggregated index status per 1.4 on Completed rows only: true indexed, false notIndexed. Never null on a result row (null aggregate => Check Failed, not in results[])
  cumulative: boolean;
  // Field-ASIN Index hit / not hit / no result
  field_asin_index: boolean | null;
  // Keyword checked (after dedupe)
  keyword: string;
  // Storefront Index hit / not hit / no result; omit when seller_id was not provided
  storefront_index?: boolean | null;
  // Traditional Index hit (true), not hit (false), or no result for this path (null)
  traditional_index: boolean | null;
}>;
  // Echo of request Seller ID; omitted when not provided.
  seller_id?: string;
} | null;
  // Set on failure.
  error: { code?: string; details?: { [key: string]: unknown; } | null; message?: string; remediation?: string | null; } | null;
}>>; };
```

## 5. `compare_asin_keywords`

Helium 10 Keyword Research (Cerebro). Multi-ASIN keyword comparison for one main ASIN vs 1–10 competitors: returns the compare scope (union of organic phrases from the main ASIN and each competitor — not intersection only) with each phrase's CPS, search volume, main organic rank, competitor coverage, and competitive average rank; the scope includes main-only keywords (cps = 0), gap/opportunity keywords, and shared competitive keywords.

When to use: call when the user's question — across multiple ASINs — involves keyword competition, shared keywords, competitor organic ranking, competitive pressure, CPS, or gap/opportunity keywords, even if they never say "CPS". Complement with get_keywords_by_asin as needed for richer per-ASIN metrics.

Not for: single-ASIN questions with no compare intent.

Edge case: the main ASIN may return no Cerebro rows — still call; results are then driven by the competitor side (rows where the main ASIN does not rank organically omit main_organic_rank).

`limit` accepts 1-10000 rows per call (default 150); up to 1000 rows per page are returned inline and a larger ask is delivered as a downloadable file instead (data.export.download_url) — set response_format='download' when the user explicitly wants a file. If data.pagination.has_more is true the result is incomplete: call again with `cursor` = data.pagination.next_cursor, but prefer narrowing the query over paginating through everything — this tool can page to at most 100,000 rows for one query, past which the result is truncated (truncation_reason='traversal_bound') and only a narrower query reaches the rest. Cursors expire 30 minutes after being issued and are bound to the query that issued them. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_compare_asin_keywords(args: {
  // 1 to 10 competitor ASINs (must NOT include main_asin). Each ASIN is 10-char alphanumeric.
  competitor_asins: Array<string>;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Opaque pagination cursor from a previous response's pagination.next_cursor. Omit for the first page. Pass it back verbatim with the same query arguments (limit may change between pages); never construct or edit it. Cursors are reusable but expire 30 minutes after being issued (pagination.cursor_expires_at) and are bound to the query that issued them.
  cursor?: string | null;
  // Optional. Default false: the search covers each ASIN's parent and all child variations (main and every competitor). Pass true to cover only the exact ASINs given.
  exclude_variations?: boolean;
  // File format when results are delivered as a download (ignored for inline delivery). 'json' (default): gzip-compressed JSON, best for programmatic analysis. 'csv': UTF-8 CSV that opens directly in Excel/Sheets - prefer it when the user wants a spreadsheet/report file. Columns are the union of row fields; nested values stay JSON-encoded.
  export_format?: "json" | "csv";
  // Number of rows to return, 1-10000 (default 150); out-of-range values are rejected. Up to 1000 rows come back inline; ask for more and the window is delivered as a file instead (data.export.download_url), so size `limit` to what is actually wanted. A page can still come back shorter than `limit` when the rows are unusually large (the ~1MB inline budget); pagination.truncated says so and `cursor` continues from there. When pagination.has_more is true, fetch the next batch with `cursor`. Do NOT probe with a small limit to learn the dataset size — every page carries the same total_count/has_more metadata, so ask for the rows you actually need and read the metadata of that response.
  limit?: number;
  // Primary ASIN under analysis (the user's product), 10-char alphanumeric (e.g. B09F9C3KFS).
  main_asin: string;
  // Marketplace code: US, UK, DE, JP, etc. Must be in the Cerebro allowlist.
  marketplace: "US" | "CA" | "MX" | "DE" | "ES" | "IT" | "FR" | "UK" | "IN" | "NL" | "AU" | "JP" | "AE" | "BR" | "SA";
  // How results are delivered. 'auto' (default): rows are returned inline when they fit the response budget, otherwise they are exported to a file and data.export.download_url is returned instead — so simply request the amount of data you need via `limit`. 'inline': always inline, oversized pages are truncated and continue via cursor. 'download': always a downloadable file, even for small results; the file contains exactly the window selected by `limit`/`cursor`, so size `limit` to what is wanted (the tool's max for the whole dataset). Use 'download' when the user wants a file or when you need many rows for analysis (fetch the link in your execution environment instead of paging through the conversation).
  response_format?: "auto" | "inline" | "download";
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{
  // Multi-ASIN CPS compare payload when the call succeeds. May be omitted or empty on failure—check for an ``error`` payload.
  data: {
  // Present when results were delivered as a downloadable file (explicit response_format=download, or auto mode when the requested rows exceed the inline budget). download_url is for the user (hand it over) or for you (download in your execution environment to analyze the full data); never paste raw file contents into the conversation.
  export?: {
  // Column names appearing across the exported rows (after any projection); sparse fields may be absent on some rows.
  columns: Array<string>;
  // Time-limited HTTPS link to the gzip-compressed JSON export.
  download_url: string;
  // ISO-8601 UTC timestamp when download_url stops working.
  expires_at: string;
  // Exported file's format: 'json.gz' (gzip-compressed JSON, default) or 'csv' (UTF-8, opens directly in Excel), per the export_format argument.
  file_format: "json.gz" | "csv";
  // Number of rows contained in the exported file.
  row_count: number;
  // The first few exported rows (size-bounded; empty when even one row exceeds the sample budget). Use together with columns to describe the file contents to the user without downloading it.
  sample_rows: Array<{ [key: string]: unknown; }>;
} | null;
  // One row per phrase in the CPS compare scope (main ASIN plus competitors — organic union). Sorted by ``cps`` descending. Optional fields with no value are omitted (not sent as null).
  keywords: Array<{
  // Competitive Avg — arithmetic mean organic rank of the competitors that rank for this phrase. Absent when no competitor ranks organically.
  competitive_avg?: number | null;
  // CPS (Competitor Performance Score), 0–10. Higher = more competitive pressure (competitors rank well and broadly). 0 = main-only word (no competitor ranks organically) or formula 0.
  cps?: number;
  // Your Rank — main ASIN organic rank for this phrase. Absent = main ASIN does not rank organically (gap word); treat a missing field the same as null.
  main_organic_rank?: number | null;
  // Keyword Phrase — search term in the CPS compare scope (main + competitor organic union).
  phrase?: string;
  // Competitors Ranking (numerator) — number of competitor ASINs that rank organically for this phrase (excludes the main ASIN).
  ranking_competitors?: number;
  // Search Volume — estimated monthly Amazon searches for this phrase.
  search_volume?: number;
  // Competitors Ranking (denominator) — total de-duplicated competitor ASINs supplied in the request (excludes the main ASIN).
  total_competitors?: number;
}>;
  // Pagination metadata for this page of rows. When has_more is true the result set is NOT complete — pass next_cursor back to fetch the next batch.
  pagination: {
  // Pagination consistency mode. 'live': every page queries live data (no snapshot); if the underlying data changes between pages, rows may shift across page boundaries - restart without `cursor` for a fresh view.
  consistency: string;
  // Rows delivered so far across this cursor chain, including this response (exported file rows count for download delivery).
  cumulative_count: number;
  // ISO-8601 UTC time when next_cursor stops being accepted; null when next_cursor is null. Expired cursors are rejected - restart from the first page by omitting `cursor`.
  cursor_expires_at: string | null;
  // True when more rows exist beyond this response.
  has_more: boolean;
  // Present only when the requested `limit` was adjusted: the value actually applied after clamping into [1, max].
  limit_applied?: number;
  // Present only when the requested `limit` was adjusted: the value as requested, before clamping.
  limit_requested?: number;
  // Opaque cursor for the next batch; pass it back verbatim as `cursor` with the same query arguments. Null when has_more is false. When truncation_reason is 'traversal_bound' this cursor is a terminal marker: passing it back returns a guidance error instead of rows - narrow the filters, and never restart from the first page to 'continue'.
  next_cursor: string | null;
  // Rows returned inline in this response (0 in download responses).
  returned_count: number;
  // Total rows matching the query, when known; null when the tail has not been reached yet (rely on has_more).
  total_count: number | null;
  // True when this response covers less than the requested window; see truncation_reason for why and how to continue.
  truncated: boolean;
  // Why truncated is true. 'size_limit': page cut to fit the inline response budget - continue with next_cursor. 'traversal_bound': this tool cannot paginate past its per-query row cap; next_cursor is a terminal marker (reusing it returns an error) - narrow the filters to reach the remaining rows, never restart from page 1. Null when truncated is false.
  truncation_reason: "size_limit" | "traversal_bound" | null;
};
  // One-sentence coverage note for this response (which rows are included and how to get the rest). Read it before concluding the data is complete.
  results_summary: string;
};
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
} | {
  // Structured error response from io-api for MCP endpoints.
  error: {
  // MCP string error code.
  code: string;
  // Optional structured error details.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Optional human-readable remediation guidance (may be omitted).
  remediation?: string;
};
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
}>>; };
```

## 6. `compare_item_keywords`

Helium 10 Keyword Research for Walmart (Walmart Cerebro). Multi-item keyword comparison for one main Walmart item vs 1-10 competitors: returns the compare scope (union of the phrases the main item and each competitor rank on, not intersection only) with each phrase's CPS, Walmart search trend score, the main item's organic rank, competitor coverage, and competitive average rank. The scope includes main-only keywords (cps = 0), gap keywords the competitors rank on but the main item does not, and shared competitive keywords. Use for: keyword competition, shared keywords, competitor organic ranking, competitive pressure, CPS, or gap/opportunity keywords across several Walmart items - even if the user never says "CPS". If which item is the main one is unclear, ask once before calling. Single-item questions with no compare intent - use get_keywords_by_item_id. Not for: Amazon products - use compare_asin_keywords instead. Edge case: the main item may return no rows of its own - still call; results are then driven by the competitor side, and rows where the main item does not rank organically omit main_organic_rank. `limit` accepts 1-10000 rows per call (default 150); up to 1000 rows per page are returned inline and a larger ask is delivered as a downloadable file instead (data.export.download_url) — set response_format='download' when the user explicitly wants a file. If data.pagination.has_more is true the result is incomplete: call again with `cursor` = data.pagination.next_cursor, but prefer narrowing the query over paginating through everything — this tool can page to at most 100,000 rows for one query, past which the result is truncated (truncation_reason='traversal_bound') and only a narrower query reaches the rest. Cursors expire 30 minutes after being issued and are bound to the query that issued them. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_compare_item_keywords(args: {
  // 1 to 10 competitor Walmart item IDs (must NOT include main_item_id). Each is all digits. De-duplicated server-side; the resulting count is total_competitors, the CPS coverage denominator.
  competitor_item_ids: Array<string>;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Opaque pagination cursor from a previous response's pagination.next_cursor. Omit for the first page. Pass it back verbatim with the same query arguments (limit may change between pages); never construct or edit it. Cursors are reusable but expire 30 minutes after being issued (pagination.cursor_expires_at) and are bound to the query that issued them.
  cursor?: string | null;
  // Optional. Default false: the search aggregates each item and all its variants (main and every competitor). Pass true to cover only the exact item IDs given.
  exclude_variations?: boolean;
  // File format when results are delivered as a download (ignored for inline delivery). 'json' (default): gzip-compressed JSON, best for programmatic analysis. 'csv': UTF-8 CSV that opens directly in Excel/Sheets - prefer it when the user wants a spreadsheet/report file. Columns are the union of row fields; nested values stay JSON-encoded.
  export_format?: "json" | "csv";
  // Number of rows to return, 1-10000 (default 150); out-of-range values are rejected. Up to 1000 rows come back inline; ask for more and the window is delivered as a file instead (data.export.download_url), so size `limit` to what is actually wanted. A page can still come back shorter than `limit` when the rows are unusually large (the ~1MB inline budget); pagination.truncated says so and `cursor` continues from there. When pagination.has_more is true, fetch the next batch with `cursor`. Do NOT probe with a small limit to learn the dataset size — every page carries the same total_count/has_more metadata, so ask for the rows you actually need and read the metadata of that response.
  limit?: number;
  // Primary Walmart item under analysis (the user's product), all digits (e.g. 10450114). It is the numeric segment at the end of a walmart.com product URL. Amazon ASINs are not accepted here - use compare_asin_keywords for Amazon.
  main_item_id: string;
  // How results are delivered. 'auto' (default): rows are returned inline when they fit the response budget, otherwise they are exported to a file and data.export.download_url is returned instead — so simply request the amount of data you need via `limit`. 'inline': always inline, oversized pages are truncated and continue via cursor. 'download': always a downloadable file, even for small results; the file contains exactly the window selected by `limit`/`cursor`, so size `limit` to what is wanted (the tool's max for the whole dataset). Use 'download' when the user wants a file or when you need many rows for analysis (fetch the link in your execution environment instead of paging through the conversation).
  response_format?: "auto" | "inline" | "download";
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{
  // Walmart multi-item CPS compare payload when the call succeeds. May be omitted or empty on failure—check for an ``error`` payload.
  data: {
  // Present when results were delivered as a downloadable file (explicit response_format=download, or auto mode when the requested rows exceed the inline budget). download_url is for the user (hand it over) or for you (download in your execution environment to analyze the full data); never paste raw file contents into the conversation.
  export?: {
  // Column names appearing across the exported rows (after any projection); sparse fields may be absent on some rows.
  columns: Array<string>;
  // Time-limited HTTPS link to the gzip-compressed JSON export.
  download_url: string;
  // ISO-8601 UTC timestamp when download_url stops working.
  expires_at: string;
  // Exported file's format: 'json.gz' (gzip-compressed JSON, default) or 'csv' (UTF-8, opens directly in Excel), per the export_format argument.
  file_format: "json.gz" | "csv";
  // Number of rows contained in the exported file.
  row_count: number;
  // The first few exported rows (size-bounded; empty when even one row exceeds the sample budget). Use together with columns to describe the file contents to the user without downloading it.
  sample_rows: Array<{ [key: string]: unknown; }>;
} | null;
  // One row per phrase in the CPS compare scope (main item plus competitors — organic union, not intersection). Sorted by ``cps`` descending, then Amazon search volume and Walmart search trend score descending. An empty compare scope returns an empty array, not an error. Optional fields with no value are omitted (not sent as null).
  keywords: Array<{
  // Search Volume — Estimated number of times this keyword is searched monthly on Amazon.
  amazon_search_volume?: number | null;
  // Competitor Rank (avg) — Average rank of competitor Products. Products that did not rank are ignored in this calculation. Absent when no competitor ranks organically.
  competitive_avg?: number | null;
  // Competitor Performance Score — Score measures, from 0-10, how relevant the keyword is for the entered competitor products (excludes first product). A higher score means more products are ranked organically, and have low average organic rank. 0 = no competitor ranks organically (main-only word) or the formula evaluates to 0.
  cps?: number;
  // Position (Rank) — Position the first Product (in a multi Product search) holds in Walmart's search results. Absent = the main item does not rank organically (gap word); treat a missing field the same as null.
  main_organic_rank?: number | null;
  // Keyword Phrase — Keyword for which the product appears in top 500 positions in Walmart search results
  phrase?: string;
  // Ranking Competitors (count) — Number of competitor Products that ranked for the given phrase.
  ranking_competitors?: number;
  // Search Trend Score — This score represents the relative popularity of search terms on Walmart, normalized on a scale from 0 to 100.
  search_trend_score?: number;
  // Competitor items this score was computed over: the de-duplicated request minus any whose Walmart lookup returned nothing (excludes the main item). Denominator of the coverage term in CPS, so it can be below the number of IDs you passed.
  total_competitors?: number;
}>;
  // Pagination metadata for this page of rows. When has_more is true the result set is NOT complete — pass next_cursor back to fetch the next batch.
  pagination: {
  // Pagination consistency mode. 'live': every page queries live data (no snapshot); if the underlying data changes between pages, rows may shift across page boundaries - restart without `cursor` for a fresh view.
  consistency: string;
  // Rows delivered so far across this cursor chain, including this response (exported file rows count for download delivery).
  cumulative_count: number;
  // ISO-8601 UTC time when next_cursor stops being accepted; null when next_cursor is null. Expired cursors are rejected - restart from the first page by omitting `cursor`.
  cursor_expires_at: string | null;
  // True when more rows exist beyond this response.
  has_more: boolean;
  // Present only when the requested `limit` was adjusted: the value actually applied after clamping into [1, max].
  limit_applied?: number;
  // Present only when the requested `limit` was adjusted: the value as requested, before clamping.
  limit_requested?: number;
  // Opaque cursor for the next batch; pass it back verbatim as `cursor` with the same query arguments. Null when has_more is false. When truncation_reason is 'traversal_bound' this cursor is a terminal marker: passing it back returns a guidance error instead of rows - narrow the filters, and never restart from the first page to 'continue'.
  next_cursor: string | null;
  // Rows returned inline in this response (0 in download responses).
  returned_count: number;
  // Total rows matching the query, when known; null when the tail has not been reached yet (rely on has_more).
  total_count: number | null;
  // True when this response covers less than the requested window; see truncation_reason for why and how to continue.
  truncated: boolean;
  // Why truncated is true. 'size_limit': page cut to fit the inline response budget - continue with next_cursor. 'traversal_bound': this tool cannot paginate past its per-query row cap; next_cursor is a terminal marker (reusing it returns an error) - narrow the filters to reach the remaining rows, never restart from page 1. Null when truncated is false.
  truncation_reason: "size_limit" | "traversal_bound" | null;
};
  // One-sentence coverage note for this response (which rows are included and how to get the rest). Read it before concluding the data is complete.
  results_summary: string;
};
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
} | {
  // Structured error response from io-api for MCP endpoints.
  error: {
  // MCP string error code.
  code: string;
  // Optional structured error details.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Optional human-readable remediation guidance (may be omitted).
  remediation?: string;
};
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
}>>; };
```

## 7. `compare_listings`

Helium 10 Listing Comparison. Returns the field set from Compare Key Metrics for every ASIN in the session — the main ASIN plus all competitor ASINs — as an array of objects under asins. The main ASIN is always first, flagged with is_main: true. Maps to the Compare Key Metrics Across ASINs table in the LA UI. Use to benchmark your listing against multiple competitors at once, see where you're ahead or behind on content and sales, and build a prioritized improvement list. Competitor ASINs that are valid 10-char codes but can't be resolved upstream are not returned as empty rows — they are split out into failed_asins as {asin, error} so the asins rows always carry real data. Not for: a single-ASIN lookup (use get_listing_details); keyword comparison (use get_top_keywords); the LQS checklist (use get_listing_score). This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_compare_listings(args: {
  // Alias for ``main_asin`` (accepted because callers often send ``asin``). Prefer ``main_asin``; when both are supplied ``main_asin`` wins. Leave unset if you already pass ``main_asin``.
  asin?: string | null;
  // Up to 10 competitor ASINs (each 10 characters). Required: ``compare_listings`` only returns rows in multi-ASIN mode. Empty / blank entries are dropped before the upstream call.
  competitor_asins: Array<string>;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Main Amazon ASIN (10 characters). Always emitted as the first row of ``asins`` with ``is_main: true``. Required — the parameter is named ``main_asin`` (not ``asin``); you may also pass it via the ``asin`` alias.
  main_asin?: string | null;
  // Optional marketplace short code where the ASINs are listed. Supported: US, CA, MX, DE, ES, IT, FR, UK, IN, NL, AE, BR, AU. Defaults to US when omitted. Case-insensitive; whitespace is trimmed.
  marketplace?: string;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{
  // Multi-mode only — same field set as ``get_listing_details.seed_data`` for every ASIN in the session (main ASIN plus all competitor ASINs). The main ASIN is always first, flagged with ``is_main: true``. Maps to the ``Compare Key Metrics Across ASINs`` table in the Listing Analyzer UI. Rows are returned under ``asins``. Partial-failure handling: a competitor ASIN that is a valid 10-char code but could not be resolved upstream (not found / no data) is NOT emitted as an empty ``asins`` row — it is split out into ``failed_asins`` with an error code, so every row in ``asins`` carries real data.
  data: {
  // Per-ASIN listing rows that resolved successfully. Main ASIN first (``is_main: true``); the remaining rows are the caller-supplied competitor ASINs in the order returned by upstream. ASINs that could not be resolved are excluded here and listed in ``failed_asins`` instead.
  asins?: Array<{
  // Age (Month) — Listing age in full calendar months from original list date through today (UTC). Null when upstream age is missing or invalid.
  age?: number | null;
  // Internal Amazon category id.
  amz_category?: number | null;
  // ASIN this row describes.
  asin?: string | null;
  // Buy-box winning seller name.
  bb_seller?: string | null;
  // Best Sales Period — Calendar month in which a product sees its highest sales volume.
  best_month?: string | null;
  // Amazon Best Sellers list URL for this product's category.
  best_sellers_url?: string | null;
  // Top-level Amazon category name for this ASIN.
  category_title?: string | null;
  // ASIN Revenue — Estimated revenue for this specific ASIN over the past 30 days (major currency units).
  child_monthly_revenue?: number | null;
  // ASIN Sales — Estimated sales for this specific ASIN over the past 30 days.
  child_monthly_sales?: number | null;
  // ASIN-level sales change indicator vs. recent baseline.
  child_sales_change?: number | null;
  // ASIN-level YoY sales delta indicator.
  child_sales_year_over_year?: number | null;
  // ASIN-level trailing 12-month sales.
  child_year_sales?: number | null;
  // Coupon Type — discount kind (e.g. ``percentage``, ``absolute``). Null when no coupon is active.
  coupon_type?: string | null;
  // Coupon Value — raw coupon-value payload, most often a nested object such as ``{amount: 5}``; occasionally a scalar. Treat it as an opaque blob. Null when no coupon is active or the value was not reported.
  coupon_value?: { [key: string]: unknown; } | number | string | null;
  // Package height (units as reported upstream).
  height?: string | null;
  // Coupon Status — whether this listing currently advertises a coupon.
  is_coupon_exists?: boolean | null;
  // True for the main ASIN (always emitted as the first array entry); false for competitor ASINs.
  is_main?: boolean | null;
  // Package length (units as reported upstream).
  length?: string | null;
  // Listing Quality Score (LQS) — 0–10 score; an analysis of this product's copy, media and reviews.
  lqs?: number | null;
  // Parent Level Revenue — Estimated revenue for all child ASINs under this parent over the past 30 days (major currency units).
  monthly_revenue?: number | null;
  // Parent Level Sales — Estimated sales for all child ASINs under this parent over the past 30 days.
  monthly_sales?: number | null;
  // Number of Images on the product listing.
  number_of_images?: number | null;
  // Number of active sellers competing on this listing.
  number_of_sellers?: number | null;
  // Price — Price at which the product is sold (already converted to major currency units, e.g. dollars).
  price?: number | null;
  // Price change indicator vs. recent baseline (upstream-defined direction code).
  price_change?: number | null;
  // Review — Total number of customer reviews.
  review_count?: number | null;
  // Rating — Amazon customer reviews rating from poor (1.0) to outstanding (5.0).
  reviews_rating?: number | null;
  // Parent Level Sales change indicator vs. recent baseline.
  sales_change?: number | null;
  // Category BSR — Best Seller Rank; Amazon-awarded rank for the main category.
  sales_rank?: number | null;
  // Sales to Reviews — Monthly Sales / Total Reviews Count (float).
  sales_to_reviews?: number | null;
  // Parent-level YoY sales delta indicator vs. last year (e.g. -13 means 13% lower than last year). Same shape as ``child_sales_year_over_year``.
  sales_year_over_year?: number | null;
  // Seller Region — Seller's country/region of origin.
  seller_country?: string | null;
  // Fulfillment — Method of storage and shipping. FBA = Fulfilled by Amazon, FBM = Fulfilled by Merchant, Amazon = Sold by Amazon (encoded as integer code).
  seller_type?: number | null;
  // Amazon size tier classification (encoded id; e.g. small standard / oversize).
  size_tier?: number | null;
  // Stock — Estimated number of units left in inventory.
  stock?: string | null;
  // Whether upstream reports an inventory cap on this listing. Null when upstream did not report it.
  stock_has_limit?: boolean | null;
  // Storage Fee — FBA storage fee per 1,000 units / month, split into ``jan_sept`` (Jan–Sept rate, USD) and ``oct_dec`` (Oct–Dec rate, USD). Only populated for US-marketplace, Fulfilled-by-Amazon listings whose size tier is in the supported FBA bucket (small/large standard, large bulky, or any of the four extra-large tiers). Null in every other case (non-US marketplace, non-FBA seller, unsupported size tier, or missing package dimensions).
  storage_fee?: {
  // Jan–Sept storage fee rate (USD).
  jan_sept?: number | null;
  // Oct–Dec storage fee rate (USD).
  oct_dec?: number | null;
} | null;
  // Subcategory entries for this ASIN. Each item carries node_id / node_name / subcategory_url / subcategory_bsr (subcategory BSR rank).
  subcategories?: Array<{
  // Subcategory browse node id (string).
  node_id?: string | null;
  // Subcategory display name.
  node_name?: string | null;
  // Subcategory Best Seller Rank for this ASIN.
  subcategory_bsr?: number | null;
  // Amazon Best Sellers URL scoped to this subcategory.
  subcategory_url?: string | null;
}> | null;
  // Per-subcategory BSR rows (alternate shape to ``subcategories``); each item has ``node_id`` and ``bsr``. ``node_id`` is the same string form as ``subcategories[*].node_id`` so consumers can join the two lists by plain string equality.
  subcategories_bsr?: Array<{
  // Best Seller Rank within this subcategory.
  bsr?: number | null;
  // Subcategory browse node id. String form (aligned with ``subcategories[*].node_id``) so the two lists can be joined with string equality.
  node_id?: string | null;
}> | null;
  // Product title for this ASIN.
  title?: string | null;
  // Top 10 Keywords — Number of keywords in which this product ranks in the top 10.
  top10_keywords?: number | null;
  // Top 10 Search Volume — Total search volume for the keywords in which this product ranks in the top 10.
  top10_search_volumes?: number | null;
  // Variation Count — Number of product variations.
  variation_count?: number | null;
  // Weight (lb) — Package weight in pounds.
  weight?: string | null;
  // Package width (units as reported upstream).
  width?: string | null;
  // Last Year Sales — Sales over the trailing 12 months.
  year_sales?: number | null;
}> | null;
  // Competitor ASINs that were syntactically valid but could not be resolved upstream (not found or no analyzable data returned — an otherwise-empty row). Each entry is ``{asin, error}``. Null / omitted when every requested ASIN resolved. The main ASIN never appears here (an unresolved main ASIN fails the whole call upstream).
  failed_asins?: Array<{
  // The competitor ASIN that could not be resolved.
  asin?: string | null;
  // Machine-readable failure reason. ``ASIN_NOT_FOUND`` when upstream returned no analyzable data for this ASIN.
  error?: string | null;
}> | null;
};
  // Always null — Listing Analyzer surfaces failures as an MCP error result (isError=true), not as an inline error payload.
  error: null;
}>>; };
```

## 8. `create_amazon_indirect_cost`

Create ONE Amazon Indirect Cost setting (a configuration/contract rule for a seller-maintained expense that is NOT an Amazon platform fee and NOT COGS — e.g. payroll, brand registration fee, product photography, offline promo). The setting is later expanded into day-level charges that feed P&L indirect_cost. Provide cost_name, amount (> 0), currency (ISO 4217, REQUIRED), date_tz (IANA, REQUIRED), frequency, starts_at, and optional ends_at / amortize_daily / scope fields. DO NOT send cost_level or cost_type — the level is INFERRED from scope fields: no seller_id -> h10_account; seller_id only -> seller; seller_id + marketplace -> marketplace; seller_id + marketplace + non-empty skus -> product. Skipping a level (marketplace without seller_id, or skus without seller_id + marketplace) -> INVALID_INPUT. Use SHORT country codes for marketplace (US, CA, UK, DE, …), never Amazon marketplace IDs or domains. For marketplace/product level, currency and date_tz must equal that marketplace's currency and report timezone. date_tz only stamps the calendar day of created_at — it is not stored on the setting and does not make the cost timezone-scoped (charges land on plain calendar dates); per-cost timezone billing is not supported. cost_name must be UNIQUE within the H10 account (duplicate -> error; not idempotent). amortize_daily=true is ignored for one_time/daily frequencies. Amazon only — Walmart/TikTok are rejected; account_id / platform are never accepted. On SUCCESS the tool returns an EMPTY object ({}) — no cost_id and no setting body: to get the new cost_id (e.g. before edit/delete) call list_amazon_indirect_cost_settings and filter by cost_name. NOT for editing (edit_amazon_indirect_cost), deleting (delete_amazon_indirect_costs), browsing settings/charges, or COGS unit costs (use the COGS tools). This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_create_amazon_indirect_cost(args: { amortize_daily?: boolean | null; amount: number; context?: string; cost_name: string; currency: "AED" | "AUD" | "BRL" | "CAD" | "CNY" | "EGP" | "EUR" | "GBP" | "INR" | "JPY" | "MXN" | "PLN" | "SAR" | "SEK" | "SGD" | "TRY" | "USD" | "ZAR"; date_tz: "Africa/Cairo" | "Africa/Johannesburg" | "America/Los_Angeles" | "America/Sao_Paulo" | "Asia/Dubai" | "Asia/Kolkata" | "Asia/Riyadh" | "Asia/Shanghai" | "Asia/Singapore" | "Asia/Tokyo" | "Australia/Sydney" | "Europe/Amsterdam" | "Europe/Brussels" | "Europe/Dublin" | "Europe/Istanbul" | "Europe/London" | "Europe/Paris" | "Europe/Stockholm" | "Europe/Warsaw"; ends_at?: string | null; frequency: "one_time" | "daily" | "weekly" | "monthly" | "quarterly" | "half_yearly" | "annually"; marketplace?: "AE" | "AU" | "BE" | "BR" | "CA" | "CN" | "DE" | "EG" | "ES" | "FR" | "IE" | "IN" | "IT" | "JP" | "MX" | "NL" | "PL" | "SA" | "SE" | "SG" | "TR" | "UK" | "US" | "ZA"; seller_id?: string | null; session_id?: string; skus?: Array<{ asin?: string | null; sku: string; }> | null; starts_at: string; }): Promise<CallToolResult<{
  // Empty object ({}) on success — no cost_id and no setting body are returned. Null on failure (see error). To obtain the new cost_id, call list_amazon_indirect_cost_settings and filter by cost_name.
  data: {} | null;
  // Null on success; error on failure.
  error?: {
  // Error code string, e.g. 'INVALID_INPUT.DATE_RANGE_INVALID'.
  code: string;
  // Additional error context keyed by field or topic.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Suggested action to resolve the error.
  remediation: string;
} | null;
  // Optional non-blocking advisory (spec §6.1). Present only when the platform attaches one — currently a quota near-limit reminder (code QUOTA_NEAR_LIMIT). Null/absent on the vast majority of responses. Independent of 'error': the call still succeeded.
  notice?: {
  // Notice code, e.g. 'QUOTA_NEAR_LIMIT'.
  code: string;
  // Human-readable advisory message for the end user.
  message: string;
  // Advisory severity, e.g. 'warning'.
  severity: string;
} | null;
}>>; };
```

## 9. `delete_amazon_cogs`

Delete one or more configured Amazon COGS records by cogs_id (batch, max 100). Obtain ids from list_amazon_cogs first. Before calling this tool, double-confirm with the user: restate what will be deleted (number of cogs_ids and, when available from the list result, seller / marketplace / SKU / period) and wait for EXPLICIT user approval. Do not call on a vague 'clean up COGS' / 'remove costs' request without that confirmation, and do not treat a host Skill's confirm-before-delete as a substitute for restating the delete target here. Only rows owned by token-authorised sellers are deleted; foreign / unknown / already-deleted ids are silently ignored (deleted_count may be < ids sent — treat as success). Deleting a middle by_period segment leaves a gap on that SKU's timeline (expected). Deleting all rows for a SKU removes it from subsequent list results (SKU becomes unconfigured). NOT for create/update (use edit_amazon_cogs, which does not use cogs_id) or browsing (use list_amazon_cogs). This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_delete_amazon_cogs(args: {
  // Opaque COGS record IDs from list_amazon_cogs (1..100). Each must be a non-empty numeric id string; empty/blank/malformed -> INVALID_INPUT (whole request rejected). Duplicates are de-duplicated server-side. IDs outside token scope / unknown / already-deleted are silently ignored (deleted_count may be < ids sent).
  cogs_ids: Array<string>;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{
  // Delete result. Null on failure.
  data: {
  // Number of COGS rows actually removed (after auth filter + de-dupe). May be 0 if every id was foreign / unknown / already deleted — that is success, not failure.
  deleted_count: number;
} | null;
  // Null on success; error on failure.
  error?: {
  // Error code string, e.g. 'INVALID_INPUT.DATE_RANGE_INVALID'.
  code: string;
  // Additional error context keyed by field or topic.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Suggested action to resolve the error.
  remediation: string;
} | null;
  // Optional non-blocking advisory (spec §6.1). Present only when the platform attaches one — currently a quota near-limit reminder (code QUOTA_NEAR_LIMIT). Null/absent on the vast majority of responses. Independent of 'error': the call still succeeded.
  notice?: {
  // Notice code, e.g. 'QUOTA_NEAR_LIMIT'.
  code: string;
  // Human-readable advisory message for the end user.
  message: string;
  // Advisory severity, e.g. 'warning'.
  severity: string;
} | null;
}>>; };
```

## 10. `delete_amazon_indirect_costs`

Delete one or more Amazon Indirect Cost settings by cost_id (batch, max 100). Obtain ids from list_amazon_indirect_cost_settings first. Before calling this tool, double-confirm with the user: restate what will be deleted (number of cost_ids and, when available from the list result, cost_name / seller / marketplace / level) and wait for EXPLICIT user approval. Do not call on a vague 'remove indirect costs' / 'clean up' request without that confirmation, and do not treat a host Skill's confirm-before-delete as a substitute for restating the delete target here. Only settings owned by token-authorised sellers are deleted; foreign / unknown / already-deleted ids are silently ignored (deleted_count may be < ids sent — treat as success). Duplicates are de-duplicated server-side. Deleting a setting removes (or stops generating) its expanded charges per backend rules. Idempotent: re-deleting the same ids returns deleted_count 0. NOT for create/edit (use create_amazon_indirect_cost / edit_amazon_indirect_cost) or browsing (settings / charges lists); COGS delete → delete_amazon_cogs. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_delete_amazon_indirect_costs(args: {
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Opaque Indirect Cost setting IDs from list_amazon_indirect_cost_settings (1..100). Each must be a non-empty numeric id string; empty / blank / malformed → INVALID_INPUT (whole request rejected). Duplicates are de-duplicated server-side. IDs outside token scope / unknown / already-deleted are silently ignored (deleted_count may be < ids sent).
  cost_ids: Array<string>;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{
  // Delete result. Null on failure.
  data: {
  // Number of Indirect Cost settings actually removed (after auth filter + de-dupe). May be 0 if every id was foreign / unknown / already deleted — that is success, not failure.
  deleted_count: number;
} | null;
  // Null on success; error on failure.
  error?: {
  // Error code string, e.g. 'INVALID_INPUT.DATE_RANGE_INVALID'.
  code: string;
  // Additional error context keyed by field or topic.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Suggested action to resolve the error.
  remediation: string;
} | null;
  // Optional non-blocking advisory (spec §6.1). Present only when the platform attaches one — currently a quota near-limit reminder (code QUOTA_NEAR_LIMIT). Null/absent on the vast majority of responses. Independent of 'error': the call still succeeded.
  notice?: {
  // Notice code, e.g. 'QUOTA_NEAR_LIMIT'.
  code: string;
  // Human-readable advisory message for the end user.
  message: string;
  // Advisory severity, e.g. 'warning'.
  severity: string;
} | null;
}>>; };
```

## 11. `delete_tracked_keywords`

Helium 10 Keyword Tracker — STOP TRACKING specific keywords on a product. Permanently removes the given keywords and their rank history from the respective tracked product. The product itself stays tracked, even when every one of its keywords is removed. Amazon only, one ASIN per call.

THIS CANNOT BE UNDONE and the historical rank data for those keywords is lost forever.

BEFORE CALLING: list the exact keywords you are about to remove and get the user's explicit confirmation. When the user's request is vague ("drop the useless ones"), present the candidates and let them confirm — never decide on their behalf.

Keywords are matched EXACTLY against the tracking list, including case, spacing and punctuation; there is no fuzzy matching or spelling correction. Call list_tracked_keywords first and pass the keyword strings exactly as returned, rather than the wording the user typed — otherwise they will come back in failed_keywords and nothing will be removed.

Keywords that are not found are reported in failed_keywords and do NOT make the call fail. To stop tracking the product entirely, use delete_tracked_product instead. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_delete_tracked_keywords(args: {
  // 10-character Amazon ASIN or an Amazon product URL containing one; JSON key `asin_or_url`. Must be the ASIN as it appears in the user's tracking list — it is matched exactly, and a product tracked as a parent with all its variations is stored under the PARENT ASIN. If the user has not provided an ASIN or product URL, ask them to supply it—do not infer, search for, or invent an ASIN.
  asin_or_url: string;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Keywords to stop tracking on this product; JSON key `keywords`. No size limit. Must match the tracking list exactly (case, spacing and punctuation included) — pass the strings returned by list_tracked_keywords verbatim. Sent as-is: repeats are reported back in duplicate_count and blank entries in failed_keywords, so every entry you send is accounted for. Do not pre-filter the list yourself.
  keywords: Array<string>;
  // Amazon marketplace short code (e.g. `US`, `UK`, `DE`, `JP`); JSON key `marketplace`. REQUIRED and has no default — every tracked product belongs to exactly one marketplace, so it cannot be omitted or inferred. Must be one of the uppercase marketplace codes exposed in this schema; lowercase or mixed case is rejected.
  marketplace: "US" | "CA" | "MX" | "BR" | "DE" | "ES" | "FR" | "IT" | "UK" | "NL" | "TR" | "IN" | "AE" | "EG" | "SA" | "SE" | "PL" | "JP" | "AU" | "SG" | "CN" | "BE" | "ZA" | "IE";
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{
  // Delete result when the call completes; interpret together with ``code``.
  data?: {
  // The tracked product the keywords were removed from.
  asin?: string | null;
  // Submitted entries dropped because the same keyword appeared earlier in the same request. Counts dropped entries, not distinct repeated keywords: sending the same keyword three times gives 2. Together with removed_count and failed_count this accounts for every keyword submitted.
  duplicate_count?: number | null;
  // Number of submitted keywords that were not found on this product and therefore not removed.
  failed_count?: number | null;
  // Submitted keywords that were not found on this product. Report these back to the user verbatim — the usual cause is a spelling or casing difference from the tracking list, not a system error.
  failed_keywords?: Array<string> | null;
  // Total keywords still tracked on this product after the call. Zero is valid — the product stays tracked.
  keywords_count?: number | null;
  // Marketplace the product is tracked in.
  marketplace?: string | null;
  // Number of keywords actually removed.
  removed_count?: number | null;
} | null;
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
}>>; };
```

## 12. `delete_tracked_product`

Helium 10 Keyword Tracker — STOP TRACKING a product. Permanently removes the product from Keyword Tracker together with ALL of its tracked keywords, its competitors, and their entire rank history. Amazon only, one ASIN per call.

THIS CANNOT BE UNDONE and the historical rank data is lost forever — re-tracking the product later starts its history from zero.

BEFORE CALLING: state the exact ASIN you are about to untrack, state that its keywords, competitors and rank history will be deleted with it, and get the user's explicit confirmation. Never infer the ASIN from a vague request.

The ASIN must be the one shown in the user's tracking list. If the product was tracked as a parent with all its variations, the tracking list holds the PARENT ASIN — passing a child ASIN returns "not tracked" and deletes nothing. Use list_tracked_keywords first when unsure.

To remove only some keywords and keep the product, use delete_tracked_keywords instead. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_delete_tracked_product(args: {
  // 10-character Amazon ASIN or an Amazon product URL containing one; JSON key `asin_or_url`. Must be the ASIN as it appears in the user's tracking list — it is matched exactly, and a product tracked as a parent with all its variations is stored under the PARENT ASIN. If the user has not provided an ASIN or product URL, ask them to supply it—do not infer, search for, or invent an ASIN.
  asin_or_url: string;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Amazon marketplace short code (e.g. `US`, `UK`, `DE`, `JP`); JSON key `marketplace`. REQUIRED and has no default — every tracked product belongs to exactly one marketplace, so it cannot be omitted or inferred. Must be one of the uppercase marketplace codes exposed in this schema; lowercase or mixed case is rejected.
  marketplace: "US" | "CA" | "MX" | "BR" | "DE" | "ES" | "FR" | "IT" | "UK" | "NL" | "TR" | "IN" | "AE" | "EG" | "SA" | "SE" | "PL" | "JP" | "AU" | "SG" | "CN" | "BE" | "ZA" | "IE";
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{
  // Delete result when the call completes; interpret together with ``code``.
  data?: {
  // Number of tracked keywords removed together with the product.
  deleted_keywords_count?: number | null;
  // Marketplace the product was tracked in.
  marketplace?: string | null;
  // The ASIN that was untracked.
  untracked_asin?: string | null;
} | null;
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
}>>; };
```

## 13. `edit_amazon_cogs`

Warning: This tool can overwrite or remove existing COGS history. Overlay updates may trim or split overlapping records, while persistent updates may clear and replace prior period records. Create or update Amazon COGS via interval overlay upsert (batch, max 100 items). Each item identifies a SKU by seller_id + marketplace (short country code) + sku and sets cogs_type, cost components, and (for by_period) an effective date interval. Describe the DESIRED new interval and costs, not 'edit row N': this is a cover-and-replace overlay, NOT row-ID targeting — there is no cogs_id on input (any cogs_id/asin/currency/total_cogs passed is ignored; currency follows marketplace; shipping_cost/tariffs omit/null → 0). New intervals overlay existing ones: overlapping history is trimmed/split, non-overlapping history preserved; writing persistent clears prior by_period rows for that SKU. Transaction is all-or-nothing. Use to set/update per-SKU unit costs before trusting net profit. NOT for reading (use list_amazon_cogs) or deleting (use delete_amazon_cogs, which deletes by cogs_id). After success call list_amazon_cogs to see the resulting timeline (overlay may split/rewrite rows into new ids). Do not mix persistent and by_period for the same SKU in one batch; by_period items for the same SKU must not overlap each other. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_edit_amazon_cogs(args: {
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Batch of COGS upsert items (1..100). Overlay upsert located by seller_id + marketplace + sku (ID-free). JSON array order is preserved; error index is 0-based into this array.
  items: Array<{
  // Configuration type for this item: by_period (time-bounded) | persistent (single cost for all dates).
  cogs_type: "by_period" | "persistent";
  // yyyy-MM-dd. Effective end (inclusive). For by_period: null/omit = unbounded future (open-ended). Ignored for persistent. by_period requires at least one concrete endpoint.
  ends_at?: string | null;
  // Short country code (e.g. US, CA, MX, UK, DE), same allowlist as list_amazon_cogs. Case-sensitive: upper-case only.
  marketplace: string;
  // Unit product (procurement) cost. Required, must be >= 0 (not null). Negative -> INVALID_INPUT.
  product_cost: number;
  // Amazon seller account ID. Must be authorised on the Pacvue token; an unauthorised seller_id fails the whole batch (no partial write).
  seller_id: string;
  // Seller-borne inbound shipping cost. Omit or null -> 0. If set, must be >= 0.
  shipping_cost?: number | null;
  // Seller SKU. ASIN is resolved server-side when available; not required on input. The SKU need not already exist in the live Amazon catalog.
  sku: string;
  // yyyy-MM-dd. Effective start (inclusive). For by_period: null/omit = unbounded past. Ignored for persistent.
  starts_at?: string | null;
  // Tariff / duty cost component. Omit or null -> 0. If set, must be >= 0.
  tariffs?: number | null;
}>;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{
  // Upsert result. Null on failure.
  data: {
  // Number of input items successfully applied (= items length on full success). NOT the number of DB segments produced by overlay split. Inspect the resulting timeline via list_amazon_cogs.
  updated_count: number;
} | null;
  // Null on success; error on failure.
  error?: {
  // Error code string, e.g. 'INVALID_INPUT.DATE_RANGE_INVALID'.
  code: string;
  // Additional error context keyed by field or topic.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Suggested action to resolve the error.
  remediation: string;
} | null;
  // Optional non-blocking advisory (spec §6.1). Present only when the platform attaches one — currently a quota near-limit reminder (code QUOTA_NEAR_LIMIT). Null/absent on the vast majority of responses. Independent of 'error': the call still succeeded.
  notice?: {
  // Notice code, e.g. 'QUOTA_NEAR_LIMIT'.
  code: string;
  // Human-readable advisory message for the end user.
  message: string;
  // Advisory severity, e.g. 'warning'.
  severity: string;
} | null;
}>>; };
```

## 14. `edit_amazon_indirect_cost`

Update an existing Amazon Indirect Cost setting identified by cost_id (obtain it from list_amazon_indirect_cost_settings). You may change ONLY cost_name, amount (> 0), starts_at, and ends_at. You CANNOT change frequency, amortize_daily, currency, date_tz, seller_id, marketplace, skus, or the level — those are fixed; to alter the SKUs / level / frequency, delete the setting (delete_amazon_indirect_costs) and create a new one (create_amazon_indirect_cost). Only your own account's settings can be edited; an unknown or foreign cost_id is an error (not silently ignored). Renaming to a cost_name that already exists in the H10 account is an error. Editing dates/amount makes the backend regenerate the setting's expanded charges — re-list with list_amazon_indirect_costs to audit. Idempotent: re-submitting the same values succeeds with no further change. On SUCCESS the tool returns an EMPTY object ({}) — no setting body. account_id / platform are never accepted; Amazon only. NOT for creating, deleting, or browsing. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_edit_amazon_indirect_cost(args: {
  // New setting amount, must be > 0. For a multi-SKU product setting this is the TOTAL, re-split across SKUs when charges regenerate.
  amount: number;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Opaque setting id from list_amazon_indirect_cost_settings. Must belong to the token's H10 account; unknown / foreign id → error (own-only).
  cost_id: string;
  // New or unchanged setting name. Must stay UNIQUE within the H10 account; renaming to an existing name → error.
  cost_name: string;
  // Effective end date, yyyy-MM-dd. Omit or null = open-ended. When both set, starts_at <= ends_at.
  ends_at?: string | null;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Effective start date, yyyy-MM-dd (no time). Required.
  starts_at: string;
}): Promise<CallToolResult<{
  // Empty object ({}) on success — no setting body is returned. Null on failure (see error). Re-list settings / charges to verify the edit.
  data: {} | null;
  // Null on success; error on failure.
  error?: {
  // Error code string, e.g. 'INVALID_INPUT.DATE_RANGE_INVALID'.
  code: string;
  // Additional error context keyed by field or topic.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Suggested action to resolve the error.
  remediation: string;
} | null;
  // Optional non-blocking advisory (spec §6.1). Present only when the platform attaches one — currently a quota near-limit reminder (code QUOTA_NEAR_LIMIT). Null/absent on the vast majority of responses. Independent of 'error': the call still succeeded.
  notice?: {
  // Notice code, e.g. 'QUOTA_NEAR_LIMIT'.
  code: string;
  // Human-readable advisory message for the end user.
  message: string;
  // Advisory severity, e.g. 'warning'.
  severity: string;
} | null;
}>>; };
```

## 15. `edit_product_monitoring`

Helium 10 Alerts — turns Alerts monitoring ON or OFF for one seller listing; action picks which. Keyed by seller_id + asin + marketplace + seller_sku. Monitoring is the prerequisite for search_alert_events / search_buybox_history to return data on an ASIN. One seller can sell the same ASIN under several SKUs, each monitored separately: read seller_sku from get_monitoring_status(asin).asin_rows[] — never guess it. action='disable' loses data: changes that happen while a listing is off are never recorded and cannot be backfilled by turning it back on, so confirm with the user before disabling. Already-collected alert history is kept. Slots count distinct ASINs, not SKUs; slot_changed says whether this call moved slot usage, and get_monitoring_status has the current totals. Returns outcome='enabled'/'disabled' when the switch was flipped, or 'already_enabled'/'already_disabled' when it was already in that state (no-op). On action='enable' only, the call can be rejected for plan quota, an ASIN the upstream cannot resolve, a missing SP notification subscription, or a lapsed SP-API authorization. Also on enable only: baseline data loads asynchronously and an ASIN the upstream rejects is turned back off, so don't call it permanent. Write tool: never retry automatically. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_edit_product_monitoring(args: {
  // Which way to switch monitoring. 'enable' starts collecting alerts for the listing; 'disable' stops it and permanently gives up the change events of the off-period. No default — state it explicitly.
  action: "enable" | "disable";
  // Amazon ASIN to switch monitoring for, 10 alphanumeric characters.
  asin: string;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Marketplace code, e.g. US.
  marketplace: string;
  // Helium 10 seller identifier.
  seller_id: string;
  // Seller SKU of the listing to switch. Required: one seller can list the same ASIN under several SKUs in a marketplace, each with its own monitoring switch. Take it from get_monitoring_status(asin).asin_rows[].seller_sku.
  seller_sku: string;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{
  // Result of switching monitoring on or off for one seller listing. Null on error.
  data: {
  // Amazon ASIN, echoed back.
  asin?: string | null;
  // Marketplace code, echoed back.
  marketplace?: string | null;
  // When monitoring reached its current state, Unix seconds (UTC). For 'enabled' the start time, for 'disabled' the stop time. On the already_* outcomes this is the earlier value, not now; null for a listing that was never monitored.
  monitoring_changed_at?: number | null;
  // What this call did. 'enabled'/'disabled' = the switch was flipped. 'already_enabled'/'already_disabled' = it was already in that state, so the call was a no-op — for disable, both 'never monitored' and 'switched off earlier' land on already_disabled. Also tells you which direction the other fields describe.
  outcome?: "enabled" | "already_enabled" | "disabled" | "already_disabled" | null;
  // Helium 10 seller identifier, echoed back.
  seller_id?: string | null;
  // Seller SKU of the listing that was switched, echoed back.
  seller_sku?: string | null;
  // Whether the plan's slot usage moved. Read the direction off outcome: with 'enabled' true means a new slot was taken, with 'disabled' true means one came back. False when another SKU of this ASIN is still monitored — slots are counted per distinct ASIN, not per SKU. Absolute slot counts come from get_monitoring_status, not from here.
  slot_changed?: boolean | null;
} | null;
  error: null;
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
} | {
  data: null;
  // Structured error response from io-api.
  error: {
  // MCP string error code.
  code: string;
  // Structured error details; null when the error carries none.
  details: { [key: string]: unknown; } | null;
  // Human-readable error message.
  message: string;
  // Human-readable remediation guidance; null when there is none.
  remediation: string | null;
};
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
}>>; };
```

## 16. `execute_ads_campaign_mgmt_actions`

Helium 10 Execute Campaign Management Actions (Campaign Mgmt, step 3 of 3, terminal, WRITE). Submits one or more ad-management actions (create / update / delete campaigns, ad groups, keywords, ...) to Helium 10 for a single platform + profileId.

### Confirmation is REQUIRED before calling
This tool mutates live advertising state. BEFORE invoking it, you MUST present the exact platform, profileId, and the full list of actions to the user and get explicit confirmation via an AskUserQuestion-style prompt. Do not call it on inferred intent, and never batch-confirm — the user must see each action. Deletes are irreversible.

### profileId (required)
profileId is mandatory. Resolve it with the Data Query tool get_ads_query_materials (materialType/level=profile). There is no materials tool in this pipeline.

### Hierarchical creation (Campaign → Ad Group → Product / Keyword)
Campaigns contain Ad Groups; Ad Groups contain Products and Keywords. When a creation response returns an id-name mapping, use those ids directly as parent references for the next-level creation — do not re-resolve them.

### Limits
maxActionsPerRequest (from get_ads_campaign_mgmt_platforms) caps the number of actions per call; split larger batches across calls.

Prerequisites: get_ads_campaign_mgmt_platforms (valid platform + actionType), get_ads_campaign_mgmt_schema (request_body shape per action), get_ads_query_materials (profileId). This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_execute_ads_campaign_mgmt_actions(args: {
  // One or more actions to execute against this platform + profileId. Each has a ``type`` (from get_ads_campaign_mgmt_platforms) and a ``request_body`` (shape from get_ads_campaign_mgmt_schema). Count must not exceed the platform's maxActionsPerRequest.
  actions: Array<{
  request_body: unknown;
  // Action type to perform (e.g. adgroup_create, campaign_update). Must be one of the actionType values advertised for this platform by get_ads_campaign_mgmt_platforms. Wire field: ``type``.
  type: string;
}>;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Platform key from get_ads_campaign_mgmt_platforms (e.g. amazon, walmart). Case-sensitive.
  platform: string;
  // Target profile/account id (required). Resolve via get_ads_query_materials (level=profile for non-DSP; advertiser for amazon-dsp; vendor_account / seller_account for commerce). Wire field: ``profileId``.
  profile_id: string;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{ data?: { [key: string]: unknown; } | null; error?: { [key: string]: unknown; } | null; }>>; };
```

## 17. `execute_ads_changelog_query`

Helium 10 Change Log Query (Change Log, terminal step). Runs the paged change-log list query for a profile and change type. Dates are DD-MM-YYYY (e.g. 30-07-2026). Filters must respect get_ads_changelog_schema: pass change_by only when change_source is auto/semi_auto; omit change_source when 'all'; target_name only when the schema marks it available. Resolve campaign_id via get_ads_query_materials(level=campaign). change_source_id takes rule ids / schedule uuids the caller already holds (AI change_source_id values come from the schema's static enum). Returns {items, total, page, perPage, hasMore}; paginate with page += 1 while hasMore is true. Prerequisites: get_ads_changelog_schema (valid filters), get_ads_query_materials (profile_id / campaign_id). This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_execute_ads_changelog_query(args: {
  // Optional campaign ids from get_ads_query_materials(level=campaign).
  campaign_id?: Array<string> | null;
  // Optional; valid only when change_source is auto/semi_auto; value from schema.changeBy.byChangeSource[change_source].
  change_by?: string | null;
  // Optional schema.changeSource value. 'all' = no filter -> OMIT. Only auto/semi_auto enable change_by.
  change_source?: string | null;
  // Optional. AI values come from schema.changeSourceId.byChangeBy.ai.static (use directly). Rule ids (hex32) and schedule uuids are account data the caller already holds.
  change_source_id?: Array<string> | null;
  // Change type from get_ads_changelog_platforms; drives which filters are valid (see get_ads_changelog_schema).
  change_type: string;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Range start, DD-MM-YYYY (e.g. 30-07-2026).
  date_from: string;
  // Range end, DD-MM-YYYY (e.g. 31-07-2026).
  date_to: string;
  // 1-based page number (default 1).
  page?: number;
  // Rows per page (1–500). Recommended: 100.
  per_page: number;
  // Platform key (amazon, walmart). Case-sensitive.
  platform: string;
  // Target profile id from get_ads_query_materials(level=profile).
  profile_id: string;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Sort order. Default '-changed_at' (newest first); only changed_at is sortable.
  sort?: string;
  // Optional keyword/PAT text. Only when schema.filters.target_name.available=true (bid / keyword / negative change types).
  target_name?: Array<string> | null;
}): Promise<CallToolResult<{ [key: string]: unknown; }>>; };
```

## 18. `execute_ads_query`

Helium 10 Run Ads Query (Data Query, step 5 of 5, terminal). Given a platform key, level, dimensions, measures, filters, date range, and optional profile_ids, runs the query and returns columns + rows (no middleware translation, truncation, or pagination). Use to retrieve advertising performance — spend, ROAS, ACoS, impressions, clicks, conversions, and more — across any supported dimension and time range. profile_ids is RECOMMENDED, not required — pass an explicit list when the user named a specific profile (resolve via get_ads_query_materials); leaving it empty / omitted is accepted by upstream and treated as 'every profile the user has access to' (tenant isolation stays enforced via the inbound bearer token). State filtering is supported: the state field and its enum are in get_ads_query_schema — add a not_in/in/eq filter to exclude or include archived/deleted/removed states per the user's intent. No default; leave state unconstrained unless the request implies a status scope. Param shapes (all field names come from get_ads_query_schema; two are commonly confused): dimensions is a list of OBJECTS [{"field":"<dimensions[].field>"}]; measures is a list of BARE STRING field names ["<measures[].field>"] (illustrative: [{"field":"campaign_id"}] vs ["spend","clicks"] — use the real fields from the schema). There is no date parameter — filter by the schema's date field via filters; the op MUST come from that field's ops list in get_ads_query_schema (platforms differ: one may use "le"/"ge", another "lte"/"gte", or only "between" — never assume an op without reading the schema first). The sort parameter is order_by (not sort). Prerequisites: steps 1–3 for a valid spec; step 4 to resolve profile/campaign ids the user named. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_execute_ads_query(args: { compare?: ({ current?: Array<string> | null; mode?: string | null; previous?: Array<string> | null; }) & ({ [key: string]: unknown; } | null); context?: string; dimensions?: Array<{ field?: string | null; granularity?: string | null; }> | null; filters?: Array<{ field?: string | null; op?: string | null; value?: unknown; }> | null; having?: Array<{ field?: string | null; op?: string | null; value?: unknown; }> | null; level: string; limit?: number | null; measures?: Array<string> | null; offset?: number | null; options?: ({ attribution_window?: string | null; to_currency?: string | null; }) & ({ [key: string]: unknown; } | null); order_by?: Array<{ direction?: string | null; field?: string | null; }> | null; platform: string; profile_ids?: Array<string> | null; session_id?: string; }): Promise<CallToolResult<{ [key: string]: unknown; }>>; };
```

## 19. `find_keywords_with_multi_source`

Helium 10 Listing Builder — builds a Keyword Bank: fetches keywords from one or more Helium 10 sources for the main ASIN and/or its competitors and returns a merged, deduplicated, ranked list (up to 300 rows), each row carrying a priority_score. `asin` and `competitor_asins` are each optional but at least one is required; both together give the best coverage. Live multi-source fetch. Use to build the keyword pool for a listing draft — multiple Helium 10 sources merged into one deduplicated list, each phrase scored for allocation priority. Not for: single-source keyword lookups (get_keywords_by_asin / get_keywords_by_keyword / search_amazon_keywords) — this tool is the multi-source aggregator, and only its merged rows carry the tiering fields Listing Builder needs, so single-source output cannot be substituted. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_find_keywords_with_multi_source(args: {
  // Main ASIN (10-character alphanumeric Amazon Standard Identification Number). OPTIONAL — provide at least one of asin / competitor_asins.
  asin?: string | null;
  // Competitor ASINs (up to 9). OPTIONAL — provide at least one of asin / competitor_asins; supplying BOTH is strongly recommended. CPS / competitor-advantage sources and ranking metrics rely on competitor data, so include competitors whenever available.
  competitor_asins?: Array<string> | null;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Reserved for future use — accepted but IGNORED in this release.
  filters?: { [key: string]: unknown; } | null;
  // REQUIRED. Marketplace short code (case-sensitive). Confirm the marketplace with the user once, before the first Listing Builder call of the session; every later Listing Builder tool reuses that same value — do NOT ask the user for it again once it is set. Supported values: US, CA, MX, DE, ES, IT, FR, UK, IN, NL, AU, JP, BE, BR.
  marketplace: "US" | "CA" | "MX" | "DE" | "ES" | "IT" | "FR" | "UK" | "IN" | "NL" | "AU" | "JP" | "BE" | "BR";
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // One or more keyword sources to fetch from. If the user has no preference, default to aba_converting_keywords + top_keywords.
  // 1. `aba_converting_keywords` (default): ABA Converting Keywords — Amazon Brand Analytics terms that convert to sales in this niche. ⚠️ Returns NO keywords unless the user's Amazon store is connected/bound in Helium 10.
  // 2. `sqp_sales_keywords`: SQP Sales Keywords — Search Query Performance terms driving the most purchases for the ASIN. ⚠️ Returns NO keywords unless the user's store is connected/bound in Helium 10 AND the main ASIN or a competitor ASIN belongs to that store.
  // 3. `top_keywords` (default): Top Keywords — the highest-traffic keywords the ASIN and its competitors already rank for.
  // 4. `opportunity_keywords`: Opportunity Keywords — high-traffic terms where the ASIN ranks weakly (ranking gaps to capture).
  // 5. `historical_top_keywords`: Historical Top Keywords — top keywords aggregated over a longer historical window.
  // 6. `top_performing_organic_keywords`: Top Performing Organic Keywords — the best organic-rank keywords for the ASIN / competitors.
  // 7. `top_performing_sponsored_keywords`: Top Performing Sponsored Keywords — the best-performing sponsored (PPC) keywords.
  sources: Array<"aba_converting_keywords" | "sqp_sales_keywords" | "top_keywords" | "opportunity_keywords" | "historical_top_keywords" | "top_performing_organic_keywords" | "top_performing_sponsored_keywords">;
}): Promise<CallToolResult<{
  // Keyword Bank payload: a merged, deduplicated, ranked list of keywords (up to 300) ready to feed generate_listing / rewrite_listing_section. Allocate keywords to slots in descending priority_score order.
  data: {
  // Ranked keyword rows (descending priority_score, capped at 300).
  keywords?: Array<{
  // ABA Total Click Share — Keyword metrics, including click & conversion rates for the top 3 ASINs. null when unknown.
  aba_total_click_share?: string | null;
  // ABA Total Conv. Share — Gain valuable insights into the top 3 clicked products for each keyword. null when unknown.
  aba_total_conv_share?: string | null;
  // Cerebro IQ Score — Score based on ratio of estimated search volume vs. number of competing products: a high score means a relatively high number of searches compared to the number of competitors; a low score means the opposite.
  cerebro_iq_score?: number | null;
  // Competing Products — Total number of products returned in a customer search using this keyword. null when unknown.
  competing_products?: string | null;
  // Competitor Rank Average — Average competitor organic rank for this keyword.
  competitor_rank_avg?: number | null;
  // CPR — Estimated number of units that need to be sold over 8 days, resulting from a search for this keyword.
  cpr?: number | null;
  // Competitor Performance Score (numeric, 0-10): how successful the listed competitors are as a group for this keyword. Render as `X/10` (max 10, 1 decimal, drop trailing .0 — e.g. 4.8 -> '4.8/10', 4.0 -> '4/10'). Omitted / null when unknown.
  cps?: number | null;
  // Keyword phrase.
  keyword?: string | null;
  // Keyword Sales — Estimated number of weekly sales (in number of units) attributed to each keyword.
  keyword_sales?: number | null;
  // Helium 10 sources this keyword appeared in (e.g. ['aba_converting_keywords', 'top_keywords']).
  keyword_sources?: Array<string | null> | null;
  // Position Rank — Position the main ASIN holds in Amazon's search results.
  position_rank?: number | null;
  // Priority Score — Server-computed allocation-priority score (higher = allocate to more prominent slots first in generate_listing). Methodology not exposed.
  priority_score?: number | null;
  // Ranking Competitors Count — Number of competitor ASINs that ranked for this keyword.
  ranking_competitors_count?: number | null;
  // Search Volume — Estimated number of times this keyword is searched monthly on Amazon.
  search_volume?: number | null;
  // My SQP purchase count for this phrase (SQP source only).
  sqp_my_purchases?: number | null;
  // Suggested PPC Bid — Suggested bid and bid range are calculated from a group of winning bids for ads that are similar to yours. You can choose to use the suggested bid, or any bid in or outside of the bid range. null when unknown.
  suggested_ppc_bid?: string | null;
  // Title Density — Number of page 1 products with the searched keyword in their title.
  title_density?: number | null;
} | null> | null;
  // Number of keyword rows returned.
  total_count?: number | null;
  // Non-fatal advisory. Non-null ONLY when total_count is below min_required (default 21). When present, surface it and prompt the user to add sources / competitor ASINs / manual keywords before calling generate_listing. The keywords are still usable.
  warning?: {
  // Warning code, e.g. 'INSUFFICIENT_KEYWORDS'.
  code?: string | null;
  // Human-readable warning.
  message?: string | null;
  // Minimum recommended phrases. The server's 21 assumes the DEFAULT config — 5 bullets x 3 keywords + title 3 + description 3; recompute caller-side (3 + bullets x 3 + 3) if you plan more bullets or wider slots, since the warning will NOT fire for a bank that is thin for a larger config.
  min_required?: number | null;
  // Suggested actions before generate_listing.
  remediation?: string | null;
  // How many phrases were actually returned.
  returned?: number | null;
} | null;
};
  // No error on success.
  error: null;
} | {
  // No data when the tool fails.
  data: null;
  // Tool execution error payload. Present when isError is true.
  error: {
  // Stable MCP tool error code.
  code: string;
  // Structured diagnostic details safe for the agent to inspect.
  details: { [key: string]: unknown; } | null;
  // Concise human-readable error message.
  message: string;
  // Actionable guidance the agent can use to correct and retry.
  remediation: string | null;
};
}>>; };
```

## 20. `generate_listing`

Helium 10 Listing Builder — AI-generates a complete Amazon listing from a keyword-to-slot allocation: `title` and `item_highlight` (Amazon's Item Highlight, generated together with the title as a matched pair), 5-10 bullet points, and a description. Live LLM call, non-idempotent; output language is derived server-side from `marketplace` (e.g. JP → Japanese, DE → German). Amazon now splits the legacy single <=200-character title into TWO fields: `title` (concise, <=75 characters) and `item_highlight` (<=125 characters). A short ~75-character title is CORRECT and compliant - do NOT lengthen it, keyword-stuff it, or treat it as incomplete; extra selling points and keywords belong in item_highlight. The two fields together replace the old long title. Use to draft complete listing copy — title, item highlight, bullets, and description — from an approved keyword-to-slot allocation. Not for: refining one section of an already-generated listing (use rewrite_listing_section). This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_generate_listing(args: { all_keywords?: Array<{ cps?: number | null; keyword_sources?: Array<string> | null; phrase: string; priority_score?: number | null; search_volume?: number | null; starred?: boolean | null; suggested?: boolean | null; }> | null; brand_asin?: string | null; bullet_keywords: Array<Array<string>>; context?: string; description_keywords?: Array<string> | null; marketplace: "US" | "CA" | "MX" | "DE" | "ES" | "IT" | "FR" | "UK" | "IN" | "NL" | "AU" | "JP" | "BE" | "BR"; max_bullet_len?: number | null; min_bullet_len?: number | null; parent_request_id?: number | null; product_info: { brand_name?: string | null; brand_voice?: string | null; category?: string | null; description?: string | null; key_features?: Array<string> | null; product_name?: string | null; target_audience?: string | null; }; rufus_asins?: Array<string> | null; session_id?: string; title_keywords?: Array<string> | null; }): Promise<CallToolResult<{
  // Listing-generation payload: the freshly generated title / item_highlight / bullets / description.
  data: {
  // Generated Amazon listing content.
  listing?: {
  // Generated listing bullet points, one entry per bullet slot supplied.
  bullet_points?: Array<string | null> | null;
  // Generated long-form product description.
  description?: string | null;
  // Generated Amazon Item Highlight (<=125 chars), produced together with the title. Together with the <=75-char title it replaces Amazon's legacy single <=200-char title.
  item_highlight?: string | null;
  // Generated Amazon listing title. <=75 chars per Amazon's current rule; a short ~75-char title is CORRECT - do NOT judge it as too short or lengthen it. Extra selling points live in item_highlight, not the title.
  title?: string | null;
} | null;
  // Generation record id for this listing — internal plumbing; never show it to the user. Pass as parent_request_id when calling rewrite_listing_section, and as request_id to sync_listing_to_amazon for validation.
  request_id?: number | null;
};
  // No error on success.
  error: null;
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
} | {
  // No data when the tool fails.
  data: null;
  // Tool execution error payload. Present when isError is true.
  error: {
  // Stable MCP tool error code.
  code: string;
  // Structured diagnostic details safe for the agent to inspect.
  details: { [key: string]: unknown; } | null;
  // Concise human-readable error message.
  message: string;
  // Actionable guidance the agent can use to correct and retry.
  remediation: string | null;
};
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
}>>; };
```

## 21. `get_account_profit_and_loss_breakdown`

Helium 10 Account P&L Breakdown (formerly Profits). Returns an account-level nested breakdown for selected breakdown_metrics only — { name, value, metrics[] } trees showing what drives each metric (which fee type, ad channel, or refund component is largest). Snapshot, not a time series. Use when a top-line number looks off and you need to see what's inside it, or to compare cost-component sizes at the store level. Not for: totals only (use get_account_profit_and_loss_summary); breakdown over time (use get_account_profit_and_loss_breakdown_series); per-product breakdown (use get_product_profit_and_loss_breakdown). A cost_of_goods or indirect_cost node of 0 may mean the cost was never configured rather than genuinely zero — verify with list_amazon_cogs / list_amazon_indirect_cost_settings before treating the cost picture as complete.Comparing two periods? Both must span the same number of days: read data.meta.period_days from each response and require them to match. A 30-day window against a 31-day one inflates every percentage delta by roughly 2pp. If they differ, align the windows, switch to daily averages, or state the difference and its effect on every percentage you report.Amounts are converted into the requested currency per marketplace before aggregation; omitting marketplace means the figures are the combined total across every authorised marketplace, so say so when reporting them. data.meta echoes both. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_account_profit_and_loss_breakdown(args: {
  // Optional. SQL IN filter by ASIN list (array of strings, e.g. ['B001234567']). Default null (all ASINs).
  asins?: Array<string> | null;
  // Optional. Only 11 metrics support sub-component breakdown: sales, units_sold, refund, advertising_cost, amazon_fees, reimbursements, cost_of_goods, indirect_cost, gross_revenue, sessions, units_returned. The other 10 (promo, estimated_payout, gross_profit, expenses, net_profit, margin, roi, tacos, ads_acos, refunds_percentage) are derived metrics — use summary tools for those. Default null = all 11.
  breakdown_metrics?: Array<string> | null;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // ISO 4217 code for amounts. Optional, defaults to USD.
  currency?: "AED" | "AUD" | "BRL" | "CAD" | "CNY" | "EGP" | "EUR" | "GBP" | "INR" | "JPY" | "MXN" | "PLN" | "SAR" | "SEK" | "SGD" | "TRY" | "USD" | "ZAR";
  // Inclusive start calendar date in date_tz, YYYY-MM-DD only (no time). Use the exact parameter name current_date_from; do not use start_date, date_from, or from_date. Must be <= current_date_to.
  current_date_from: string;
  // Inclusive end calendar date in date_tz, YYYY-MM-DD only (no time). Use the exact parameter name current_date_to; do not use end_date, date_to, or to_date. Must be >= current_date_from. A value after today in date_tz is silently clamped to today in date_tz (the future has no data).
  current_date_to: string;
  // Timezone used to interpret calendar dates. Must be one of the Amazon marketplace report timezones (not any IANA timezone). Examples: America/Los_Angeles, Europe/Paris, Europe/London. Italy/Germany/Spain use Europe/Paris (not Europe/Rome or Europe/Berlin). Do not pass a city name, abbreviation, or UTC offset. Optional; when omitted, defaults to the queried marketplace's report timezone (America/Los_Angeles when no single marketplace is set).
  date_tz?: "Africa/Cairo" | "Africa/Johannesburg" | "America/Los_Angeles" | "America/Sao_Paulo" | "Asia/Dubai" | "Asia/Kolkata" | "Asia/Riyadh" | "Asia/Shanghai" | "Asia/Singapore" | "Asia/Tokyo" | "Australia/Sydney" | "Europe/Amsterdam" | "Europe/Brussels" | "Europe/Dublin" | "Europe/Istanbul" | "Europe/London" | "Europe/Paris" | "Europe/Stockholm" | "Europe/Warsaw";
  // Optional marketplace filter as an array of region short codes ONLY (e.g. ['US', 'CA', 'MX']); Amazon SP-API marketplace IDs (e.g. 'ATVPDKIKX0DER'), seller IDs, and domains are rejected. Allowed: AE, AU, BE, BR, CA, CN, DE, EG, ES, FR, IE, IN, IT, JP, MX, NL, PL, SA, SE, SG, TR, UK, US, ZA. Default null (all authorised marketplaces).
  marketplace?: Array<string> | null;
  // Optional. SQL IN filter on seller account IDs (array of strings). Profits intersects with sellers from Pacvue token; no intersection → empty result. Default null (all authorised sellers).
  seller_ids?: Array<string> | null;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Optional. SQL IN filter by seller SKU list (array of strings). Default null (all SKUs).
  skus?: Array<string> | null;
}): Promise<CallToolResult>; };
```

## 22. `get_account_profit_and_loss_breakdown_series`

Helium 10 Account P&L Breakdown Trends (formerly Profits). Returns an account-level breakdown time series for selected breakdown_metrics — nodes carry values maps, children (e.g. ad channels) carry trend values; bucketed by interval. Use when you need drill-down detail and over-time trends together — e.g. Sponsored Products vs Sponsored Brands spend week over week. Not for: a snapshot breakdown (use get_account_profit_and_loss_breakdown); Level 1 trends only (use get_account_profit_and_loss_summary_series); product breakdown trends (use get_product_profit_and_loss_breakdown_series). A cost_of_goods or indirect_cost node of 0 may mean the cost was never configured rather than genuinely zero — verify with list_amazon_cogs / list_amazon_indirect_cost_settings before treating the cost picture as complete.Comparing two periods? Both must span the same number of days: read data.meta.period_days from each response and require them to match. A 30-day window against a 31-day one inflates every percentage delta by roughly 2pp. If they differ, align the windows, switch to daily averages, or state the difference and its effect on every percentage you report.Amounts are converted into the requested currency per marketplace before aggregation; omitting marketplace means the figures are the combined total across every authorised marketplace, so say so when reporting them. data.meta echoes both. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_account_profit_and_loss_breakdown_series(args: { asins?: Array<string> | null; breakdown_metrics?: Array<string> | null; context?: string; currency?: "AED" | "AUD" | "BRL" | "CAD" | "CNY" | "EGP" | "EUR" | "GBP" | "INR" | "JPY" | "MXN" | "PLN" | "SAR" | "SEK" | "SGD" | "TRY" | "USD" | "ZAR"; current_date_from: string; current_date_to: string; date_tz?: "Africa/Cairo" | "Africa/Johannesburg" | "America/Los_Angeles" | "America/Sao_Paulo" | "Asia/Dubai" | "Asia/Kolkata" | "Asia/Riyadh" | "Asia/Shanghai" | "Asia/Singapore" | "Asia/Tokyo" | "Australia/Sydney" | "Europe/Amsterdam" | "Europe/Brussels" | "Europe/Dublin" | "Europe/Istanbul" | "Europe/London" | "Europe/Paris" | "Europe/Stockholm" | "Europe/Warsaw"; granularity: "day" | "week" | "month"; marketplace?: Array<string> | null; seller_ids?: Array<string> | null; session_id?: string; skus?: Array<string> | null; }): Promise<CallToolResult>; };
```

## 23. `get_account_profit_and_loss_summary`

Helium 10 Account P&L Summary (formerly Profits). For a single date range, returns rolled-up account/store-level P&L — all Level 1 metrics (revenue, COGS, FBA & referral fees, ad spend, refunds, net margin, etc.) as a snapshot map; no product rows, no time buckets, no breakdown trees. Use for overall business performance (total profit, total fees, net-margin %) before drilling into products, trends, or fee line items. Prefer this first unless the question needs product granularity, a time series, or drill-down. Not for: per-product figures (use get_product_profit_and_loss_summary); trends (use get_account_profit_and_loss_summary_series); fee/ad/refund components (use get_account_profit_and_loss_breakdown). A cost_of_goods or indirect_cost of 0 may mean the cost was never configured rather than genuinely zero — data.caveats flags that when it happens. Verify with list_amazon_cogs / list_amazon_indirect_cost_settings before describing the figures as normal: net_profit and margin are upper bounds until you do.Comparing two periods? Both must span the same number of days: read data.meta.period_days from each response and require them to match. A 30-day window against a 31-day one inflates every percentage delta by roughly 2pp. If they differ, align the windows, switch to daily averages, or state the difference and its effect on every percentage you report.Amounts are converted into the requested currency per marketplace before aggregation; omitting marketplace means the figures are the combined total across every authorised marketplace, so say so when reporting them. data.meta echoes both. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_account_profit_and_loss_summary(args: {
  // Optional. SQL IN filter by ASIN list (array of strings, e.g. ['B001234567']). Default null (all ASINs).
  asins?: Array<string> | null;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // ISO 4217 code for amounts. Optional, defaults to USD.
  currency?: "AED" | "AUD" | "BRL" | "CAD" | "CNY" | "EGP" | "EUR" | "GBP" | "INR" | "JPY" | "MXN" | "PLN" | "SAR" | "SEK" | "SGD" | "TRY" | "USD" | "ZAR";
  // Inclusive start calendar date in date_tz, YYYY-MM-DD only (no time). Use the exact parameter name current_date_from; do not use start_date, date_from, or from_date. Must be <= current_date_to.
  current_date_from: string;
  // Inclusive end calendar date in date_tz, YYYY-MM-DD only (no time). Use the exact parameter name current_date_to; do not use end_date, date_to, or to_date. Must be >= current_date_from. A value after today in date_tz is silently clamped to today in date_tz (the future has no data).
  current_date_to: string;
  // Timezone used to interpret calendar dates. Must be one of the Amazon marketplace report timezones (not any IANA timezone). Examples: America/Los_Angeles, Europe/Paris, Europe/London. Italy/Germany/Spain use Europe/Paris (not Europe/Rome or Europe/Berlin). Do not pass a city name, abbreviation, or UTC offset. Optional; when omitted, defaults to the queried marketplace's report timezone (America/Los_Angeles when no single marketplace is set).
  date_tz?: "Africa/Cairo" | "Africa/Johannesburg" | "America/Los_Angeles" | "America/Sao_Paulo" | "Asia/Dubai" | "Asia/Kolkata" | "Asia/Riyadh" | "Asia/Shanghai" | "Asia/Singapore" | "Asia/Tokyo" | "Australia/Sydney" | "Europe/Amsterdam" | "Europe/Brussels" | "Europe/Dublin" | "Europe/Istanbul" | "Europe/London" | "Europe/Paris" | "Europe/Stockholm" | "Europe/Warsaw";
  // Optional marketplace filter as an array of region short codes ONLY (e.g. ['US', 'CA', 'MX']); Amazon SP-API marketplace IDs (e.g. 'ATVPDKIKX0DER'), seller IDs, and domains are rejected. Allowed: AE, AU, BE, BR, CA, CN, DE, EG, ES, FR, IE, IN, IT, JP, MX, NL, PL, SA, SE, SG, TR, UK, US, ZA. Default null (all authorised marketplaces).
  marketplace?: Array<string> | null;
  // Optional. SQL IN filter on seller account IDs (array of strings). Profits intersects with sellers from Pacvue token; no intersection → empty result. Default null (all authorised sellers).
  seller_ids?: Array<string> | null;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Optional. SQL IN filter by seller SKU list (array of strings). Default null (all SKUs).
  skus?: Array<string> | null;
}): Promise<CallToolResult<{
  // Account-level P&L totals for the requested date range. Null when no matching data exists (e.g. no authorised sellers intersect the seller_ids filter).
  data: {
  // Zero-cost ambiguities detected in this response (HP-2033). Absent when there is nothing to flag. A cost metric of 0 does not establish that the cost is really zero — it is equally consistent with the seller never having configured it — so each entry names the distorted metrics and the tools that settle it. Resolve these before characterising the profit figures.
  caveats?: Array<{ affects?: Array<string>; code: string; message: string; metric: string; severity: string; verify_with?: Array<string>; }>;
  // What the returned amounts cover. period_days is the inclusive length of the window actually queried — compare it against the other period before quoting any percentage change.
  meta?: {
  // ISO-4217 currency every amount is expressed in. Local-currency amounts are converted into it per marketplace before aggregation, using one rate for the whole range rather than each day's — so a period-over-period change can carry FX movement.
  currency?: string;
  date_tz?: string;
  display_ready?: boolean;
  // Always true. Amounts are already converted into `currency`; a multi-marketplace total is not a mixed-currency sum. Do not claim the figures are unconverted (HP-2034).
  fx_converted?: boolean;
  // Marketplace short codes covered, or 'all_authorised' = the combined total across every authorised marketplace. Say so when reporting, since the caller did not choose that.
  marketplaces?: unknown;
  // Inclusive day count of the queried window. Comparing a 30-day window with a 31-day one inflates every percentage delta by roughly 2pp — require both periods to match, or switch to daily averages, or state the difference.
  period_days?: number;
  // First day queried (YYYY-MM-DD).
  period_from?: string;
  // Last day queried (YYYY-MM-DD).
  period_to?: string;
  scale?: number;
  unit?: string;
};
  // Account-level P&L totals for the requested date range. Returns an object with up to 21 Level-1 metric keys when data is available, or an empty array ([]) when no data exists.
  metrics: {
  // ACoS (attr 1 day): ACOS based on Profits ad sales — 1-day attribution, SKU-level SP/SD only. Use mainly for profit, same-day sales, or organic sales analysis. For general advertising performance, prefer Ads ACOS. e.g. 21.01 means 21.01%.
  ads_acos?: number;
  // Total ad spend — Sponsored Products, Brands, Display (float, 2 dp, requested currency). Negative value (expenditure).
  advertising_cost?: number;
  // All Amazon platform fees — FBA, referral, etc. (float, 2 dp, requested currency). Negative value (expenditure).
  amazon_fees?: number;
  // Seller-defined cost of goods sold / COGS (float, 2 dp, requested currency). Negative value (expenditure).
  cost_of_goods?: number;
  // Estimated settlement payout from Amazon (float, 2 dp, requested currency).
  estimated_payout?: number;
  // Total operating expenses (float, 2 dp, requested currency). Negative value (expenditure).
  expenses?: number;
  // gross_revenue minus amazon_fees and cost_of_goods (float, 2 dp, requested currency).
  gross_profit?: number;
  // Top-line revenue: sales minus refunds (float, 2 dp, requested currency).
  gross_revenue?: number;
  // Overhead / indirect costs allocated to this period (float, 2 dp, requested currency). Negative value (expenditure).
  indirect_cost?: number;
  // Net profit margin: net_profit / gross_revenue × 100. e.g. 21.01 means 21.01%.
  margin?: number;
  // Bottom-line profit after all deductions (float, 2 dp, requested currency).
  net_profit?: number;
  // Promotional discounts (float, 2 dp, requested currency). Negative value (expenditure).
  promo?: number;
  // Total refund/return amount (float, 2 dp, requested currency). Negative value (expenditure).
  refund?: number;
  // Refund rate: refund / gross_revenue × 100. e.g. 21.01 means 21.01%.
  refunds_percentage?: number;
  // Amazon reimbursement credits (float, 2 dp, requested currency).
  reimbursements?: number;
  // Return on investment: net_profit / cost_of_goods × 100. e.g. 21.01 means 21.01%.
  roi?: number;
  // Gross sales revenue before deductions (float, 2 dp, requested currency).
  sales?: number;
  // Unique visitor sessions on product detail pages (integer, e.g. 80).
  sessions?: number;
  // TACoS (attr 1 day): ad spend / total sales, using Profits 1-day attribution for ad spend alignment. Use to measure ad spend impact on total sales and profitability. advertising_cost / gross_revenue × 100. e.g. 21.01 means 21.01%.
  tacos?: number;
  // Total units returned/refunded (integer, e.g. 2).
  units_returned?: number;
  // Total units ordered (integer, e.g. 6).
  units_sold?: number;
} | unknown[];
} | null;
  // Null on success; error object on failure.
  error?: {
  // Error code string, e.g. 'INVALID_INPUT.DATE_RANGE_INVALID'.
  code: string;
  // Additional error context keyed by field or topic.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Suggested action to resolve the error.
  remediation: string;
} | null;
  // Optional non-blocking advisory (spec §6.1). Present only when the platform attaches one — currently a quota near-limit reminder (code QUOTA_NEAR_LIMIT). Null/absent on the vast majority of responses. Independent of 'error': the call still succeeded.
  notice?: {
  // Notice code, e.g. 'QUOTA_NEAR_LIMIT'.
  code: string;
  // Human-readable advisory message for the end user.
  message: string;
  // Advisory severity, e.g. 'warning'.
  severity: string;
} | null;
}>>; };
```

## 24. `get_account_profit_and_loss_summary_series`

Helium 10 Account P&L Trends (formerly Profits). Returns account-level Level 1 P&L as a time series bucketed by granularity (day/week/month) — each metric as one series (metric_key, values); no breakdowns, no product rows. Use to visualize how the overall business trends, compare period-over-period, and pinpoint when a metric shifted. Not for: single-period totals (use get_account_profit_and_loss_summary); per-product trends (use get_product_profit_and_loss_summary_series); fee/ad sub-component trends (use get_account_profit_and_loss_breakdown_series). A cost_of_goods or indirect_cost series that is 0 in every bucket may mean the cost was never configured rather than genuinely zero — data.caveats flags that when it happens. Verify with list_amazon_cogs / list_amazon_indirect_cost_settings before describing the figures as normal: net_profit and margin are upper bounds until you do.Comparing two periods? Both must span the same number of days: read data.meta.period_days from each response and require them to match. A 30-day window against a 31-day one inflates every percentage delta by roughly 2pp. If they differ, align the windows, switch to daily averages, or state the difference and its effect on every percentage you report.Amounts are converted into the requested currency per marketplace before aggregation; omitting marketplace means the figures are the combined total across every authorised marketplace, so say so when reporting them. data.meta echoes both. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_account_profit_and_loss_summary_series(args: {
  // Optional. SQL IN filter by ASIN list (array of strings, e.g. ['B001234567']). Default null (all ASINs).
  asins?: Array<string> | null;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // ISO 4217 code for amounts. Optional, defaults to USD.
  currency?: "AED" | "AUD" | "BRL" | "CAD" | "CNY" | "EGP" | "EUR" | "GBP" | "INR" | "JPY" | "MXN" | "PLN" | "SAR" | "SEK" | "SGD" | "TRY" | "USD" | "ZAR";
  // Inclusive start calendar date in date_tz, YYYY-MM-DD only (no time). Use the exact parameter name current_date_from; do not use start_date, date_from, or from_date. Must be <= current_date_to.
  current_date_from: string;
  // Inclusive end calendar date in date_tz, YYYY-MM-DD only (no time). Use the exact parameter name current_date_to; do not use end_date, date_to, or to_date. Must be >= current_date_from. A value after today in date_tz is silently clamped to today in date_tz (the future has no data).
  current_date_to: string;
  // Timezone used to interpret calendar dates. Must be one of the Amazon marketplace report timezones (not any IANA timezone). Examples: America/Los_Angeles, Europe/Paris, Europe/London. Italy/Germany/Spain use Europe/Paris (not Europe/Rome or Europe/Berlin). Do not pass a city name, abbreviation, or UTC offset. Optional; when omitted, defaults to the queried marketplace's report timezone (America/Los_Angeles when no single marketplace is set).
  date_tz?: "Africa/Cairo" | "Africa/Johannesburg" | "America/Los_Angeles" | "America/Sao_Paulo" | "Asia/Dubai" | "Asia/Kolkata" | "Asia/Riyadh" | "Asia/Shanghai" | "Asia/Singapore" | "Asia/Tokyo" | "Australia/Sydney" | "Europe/Amsterdam" | "Europe/Brussels" | "Europe/Dublin" | "Europe/Istanbul" | "Europe/London" | "Europe/Paris" | "Europe/Stockholm" | "Europe/Warsaw";
  // Time-bucket size for series tools: day | week | month. Use the exact parameter name granularity; do not use scope, interval, period, or bucket as input aliases. Week/month align to calendar boundaries (week starts Monday; month starts on the 1st); first/last buckets may be partial. The first bucket key may precede current_date_from (e.g. from 2026-04-01 at week granularity → first key 2026-03-30). Partial buckets include only data inside the requested date range. Series responses no longer include the scope or offset fields (removed in HP-1495); use the request granularity you sent.
  granularity: "day" | "week" | "month";
  // Optional marketplace filter as an array of region short codes ONLY (e.g. ['US', 'CA', 'MX']); Amazon SP-API marketplace IDs (e.g. 'ATVPDKIKX0DER'), seller IDs, and domains are rejected. Allowed: AE, AU, BE, BR, CA, CN, DE, EG, ES, FR, IE, IN, IT, JP, MX, NL, PL, SA, SE, SG, TR, UK, US, ZA. Default null (all authorised marketplaces).
  marketplace?: Array<string> | null;
  // Optional. SQL IN filter on seller account IDs (array of strings). Profits intersects with sellers from Pacvue token; no intersection → empty result. Default null (all authorised sellers).
  seller_ids?: Array<string> | null;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Optional. SQL IN filter by seller SKU list (array of strings). Default null (all SKUs).
  skus?: Array<string> | null;
}): Promise<CallToolResult<{
  // Account-level P&L time series bucketed by the requested granularity. Null when no matching data exists (e.g. seller_ids filter yields no authorised sellers).
  data: {
  // Zero-cost ambiguities detected in this response (HP-2033). Absent when there is nothing to flag. A cost metric of 0 does not establish that the cost is really zero — it is equally consistent with the seller never having configured it — so each entry names the distorted metrics and the tools that settle it. Resolve these before characterising the profit figures.
  caveats?: Array<{
  // Metric keys present in this response whose values are distorted while the zero is unexplained. Do not report them as reliable until verified.
  affects?: Array<string>;
  // Caveat code, e.g. 'ZERO_COST_MAY_BE_UNCONFIGURED'.
  code: string;
  // What the zero does and does not establish, and what it distorts.
  message: string;
  // The zero metric this caveat is about, e.g. 'indirect_cost'.
  metric: string;
  // Advisory severity, e.g. 'warning'.
  severity: string;
  // Tools that settle whether the cost is configured. Call before concluding.
  verify_with?: Array<string>;
}> | null;
  // What the returned amounts cover: currency/unit, the resolved timezone and marketplaces, and the window actually queried. Check period_days against the other period before quoting any percentage change (HP-2032).
  meta?: {
  // ISO-4217 currency every amount is expressed in. Amounts originate in each marketplace's local currency and are CONVERTED into this one per marketplace before being aggregated — figures spanning several marketplaces are therefore comparable, not a raw sum of mixed currencies. One exchange rate is applied across the whole requested range rather than each day's historical rate, so a period-over-period change can carry some FX movement that is not a change in the underlying business.
  currency?: string | null;
  // IANA timezone the day boundaries were resolved in.
  date_tz?: string | null;
  // True: amounts can be shown as-is, no /100.
  display_ready?: boolean | null;
  // Always true for these tools. Every amount has already been converted into `currency`; none is a raw local-marketplace figure, and a multi-marketplace total is NOT a mixed-currency sum. Do not state or imply otherwise — a response carrying this flag has been checked against the backend, and claiming the figures are unconverted contradicts the payload.
  fx_converted?: boolean | null;
  // Marketplace short codes the amounts cover, or 'all_authorised' when the request did not narrow it. 'all_authorised' means the figures are the combined total across EVERY marketplace this account is authorised for, not one storefront — say so when reporting them, since the caller did not choose that. Converted into a single currency first (see currency).
  marketplaces?: unknown | null;
  // Inclusive length of the queried window in days. When comparing two periods, read this from BOTH responses and require them to be equal before quoting any percentage change: comparing a 30-day window with a 31-day one inflates every delta (~2pp on a monthly comparison). If they differ, align the windows, switch to daily averages, or state the difference and its effect on every percentage reported.
  period_days?: number | null;
  // First day actually queried (YYYY-MM-DD), after validation and the future-end-day clamp. May differ from what was requested.
  period_from?: string | null;
  // Last day actually queried (YYYY-MM-DD), after validation and the future-end-day clamp. May differ from what was requested.
  period_to?: string | null;
  // Always 1 — no rescaling applied.
  scale?: number | null;
  // Always 'major' — amounts are not in cents.
  unit?: string | null;
} | null;
  // One SeriesEntry per Level-1 metric key, covering the requested date range bucketed by the granularity parameter. All 21 Level-1 metrics are returned when data is available: sales, units_sold, refund, promo, advertising_cost, amazon_fees, reimbursements, estimated_payout, cost_of_goods, gross_revenue, gross_profit, expenses, indirect_cost, net_profit, margin, roi, tacos, ads_acos, refunds_percentage, sessions, units_returned. Each entry's 'values' map contains one key per time bucket (YYYY-MM-DD period start).
  metrics: Array<{
  // Metric identifier corresponding to a Level-1 P&L metric key, e.g. gross_revenue, net_profit, advertising_cost. Full catalogue of 21 possible values: sales, units_sold, refund, promo, advertising_cost, amazon_fees, reimbursements, estimated_payout, cost_of_goods, gross_revenue, gross_profit, expenses, indirect_cost, net_profit, margin, roi, tacos, ads_acos, refunds_percentage, sessions, units_returned.
  metric_key: string;
  // Ordered map of period-start-date (YYYY-MM-DD) → metric value per time bucket. Monetary: float, 2 dp. Rates: float percentage (21.01 = 21.01%). Counts (units_sold, units_returned, sessions): float (e.g. 6.0). null = unavailable data point. SIGN CONVENTION: expenditure metrics (refund, promo, advertising_cost, amazon_fees, cost_of_goods, expenses, indirect_cost) are NEGATIVE values (same as summary tools). Count metrics (units_sold, units_returned, sessions) are integers.
  values: { [key: string]: number | number | null; };
}>;
} | null;
  // Null on success; error object on failure.
  error?: {
  // Error code string, e.g. 'INVALID_INPUT.DATE_RANGE_INVALID'.
  code: string;
  // Additional error context keyed by field or topic.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Suggested action to resolve the error.
  remediation: string;
} | null;
  // Optional non-blocking advisory (spec §6.1). Present only when the platform attaches one — currently a quota near-limit reminder (code QUOTA_NEAR_LIMIT). Null/absent on the vast majority of responses. Independent of 'error': the call still succeeded.
  notice?: {
  // Notice code, e.g. 'QUOTA_NEAR_LIMIT'.
  code: string;
  // Human-readable advisory message for the end user.
  message: string;
  // Advisory severity, e.g. 'warning'.
  severity: string;
} | null;
}>>; };
```

## 25. `get_ads_campaign_mgmt_platforms`

Helium 10 Campaign Management Platforms (Campaign Mgmt, step 1 of 3). Lists the advertising platforms available for ad management (e.g. amazon, walmart) and, for each, the supported action types (campaign_create, keyword_update, ...). The entry point: use it to discover valid platform keys and action types before steps 2–3. Next: get_ads_campaign_mgmt_schema(platform, actionType).

### Campaign Management pipeline

① get_ads_campaign_mgmt_platforms → ② get_ads_campaign_mgmt_schema → ③ resolve profileId via get_ads_query_materials → ④ execute_ads_campaign_mgmt_actions.

Note: there is no dedicated materials tool in this pipeline — resolve the profileId (or advertiser / vendor_account / seller_account id, per the platform) with the Data Query tool get_ads_query_materials before calling execute_ads_campaign_mgmt_actions. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_ads_campaign_mgmt_platforms(args: {
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{ [key: string]: unknown; }>>; };
```

## 26. `get_ads_campaign_mgmt_schema`

Helium 10 Campaign Management Action Schema (Campaign Mgmt, step 2 of 3). Given a platform key and an action type, returns the JSON Schema needed to execute that specific action on a given platform.Use to learn what the request_body of an action must contain before calling execute_ads_campaign_mgmt_actions. Prerequisite: get_ads_campaign_mgmt_platforms (for valid platform + actionType pairs). Next: resolve profileId via get_ads_query_materials, then execute_ads_campaign_mgmt_actions. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_ads_campaign_mgmt_schema(args: {
  // Action type from get_ads_campaign_mgmt_platforms (actions[].actionType), e.g. campaign_create, keyword_update. Case-sensitive. Wire param: ``actionType``.
  action_type: string;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Platform key from get_ads_campaign_mgmt_platforms (e.g. amazon, walmart). Case-sensitive.
  platform: string;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{ [key: string]: unknown; }>>; };
```

## 27. `get_ads_changelog_platforms`

Helium 10 Change Log Platforms (Change Log, step 1). Lists the advertising platforms available for change-log queries (e.g. amazon, walmart) and, for each, the supported change types (budget_changed, bid_changed, keyword_changed, ...). The entry point: use it to discover valid platform keys and change types before querying change-log data. Call with no arguments to list every platform, or pass ``platform`` to restrict the result to one. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_ads_changelog_platforms(args: {
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Optional. Restrict the result to one platform. Must be a plain string: 'amazon' or 'walmart'. Do not pass an array or object.
  platform?: string | null;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{ [key: string]: unknown; }>>; };
```

## 28. `get_ads_changelog_schema`

Helium 10 Change Log Schema (Change Log, step 2). Given a platform key and a change type, returns the full self-describing schema for querying that change type: available filters, the change_source / change_by / change_source_id cascade, result columns, and the sort / paging contracts. Use it to learn which filters and option values are valid before querying change-log data. If supported=false, read issues and stop — do not proceed. Prerequisite: get_ads_changelog_platforms (for valid platform + change_type). This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_ads_changelog_schema(args: {
  // Change type from get_ads_changelog_platforms (changeTypes[].changeType), e.g. bid_changed. Case-sensitive.
  change_type: string;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Platform key from get_ads_changelog_platforms (amazon, walmart). Required. Case-sensitive.
  platform: string;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{ [key: string]: unknown; }>>; };
```

## 29. `get_ads_query_list`

Helium 10 Ad Data Hierarchy (Data Query, step 2 of 5). Given a platform key, returns its full level hierarchy (root → leaf, e.g. profile → campaign → adgroup → target) plus cross-level attribute and tag tables. Use to understand the data structure and identify the correct level name for the schema step. Prerequisite: get_ads_query_platforms. Next: get_ads_query_schema(platform, level). This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_ads_query_list(args: {
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // A platform key returned by get_ads_query_platforms (e.g. amazon, walmart). Case-sensitive.
  platform: string;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{ [key: string]: unknown; }>>; };
```

## 30. `get_ads_query_materials`

Helium 10 Ad Campaign Lookup (Data Query, step 4 of 5). Given a platform, a level (typically profile / campaign / adgroup / target — see get_ads_query_list) and the entity dimensions you want returned, lists the resolved stable ids in your Ads account. Use to convert profile/campaign names into the ids execute_ads_query needs. Pass a name-substring filter (e.g. {field: "campaign_name", op: "like", value: "Brand%"}) to behave like a target lookup. dimensions must be a list of OBJECTS (e.g. [{"field":"campaign_id"},{"field":"campaign_name"}], illustrative — use real fields from get_ads_query_schema), not bare strings. profile_ids is OPTIONAL — the upstream accepts an empty list and interprets it as 'every profile the user has access to' (tenant isolation stays enforced via the inbound bearer token); pass an explicit list only when the user named a specific profile. Internally this targets the same POST /query endpoint as execute_ads_query with measures / having / orderBy hard-coded to []. Prerequisite: get_ads_query_schema (for valid dimension and filter fields). Next: execute_ads_query. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_ads_query_materials(args: { context?: string; dimensions: Array<{ field?: string | null; }>; filters?: Array<{ field?: string | null; op?: string | null; value?: unknown; }> | null; level: string; limit?: number | null; offset?: number | null; platform: string; profile_ids?: Array<string> | null; session_id?: string; }): Promise<CallToolResult<{ [key: string]: unknown; }>>; };
```

## 31. `get_ads_query_platforms`

Helium 10 Available Ad Platforms (Data Query, step 1 of 5). Returns the advertising platforms available to query in your account (e.g. amazon, walmart). No inputs required — platforms are derived from your account credentials (the inbound bearer token). The entry point: use it to get valid platform keys before steps 2–5. Not for: actual performance numbers (use execute_ads_query after the intermediate steps). Next: get_ads_query_list(platform).

### Data Query pipeline

① get_ads_query_platforms → ② get_ads_query_list → ③ get_ads_query_schema → ④ get_ads_query_materials → ⑤ user selects specific profile(s) → ⑥ execute_ads_query

### Required profile selection

Before calling execute_ads_query, the user must choose specific profiles from the profile/material results.

1. Call get_ads_query_materials for the relevant scope (profile, advertiser, vendor_account, or seller_account).
2. Build a concise selection list from the returned materials. Each option should include the display name plus the id that will be sent to execute_ads_query.
3. Use Cursor's AskQuestion tool with multi-select enabled when available so the user gets a real choice list. Do not proceed from a plain text assumption.
4. Continue only after the user selects at least one option. Send only the selected ids in profile_ids or the matching scope id field.
5. Never call execute_ads_query with all profiles by default, a guessed default profile, or profiles inferred only from prior context.

If the returned material list is too large, show the most relevant profiles first and include an 'Other profile id' option. If there is only one returned profile, still ask the user to confirm that profile before executing. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_ads_query_platforms(args: {
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{ [key: string]: unknown; }>>; };
```

## 32. `get_ads_query_schema`

Helium 10 Ad Query Schema (Data Query, step 3 of 5). Given a platform key and a level, returns the full queryable definition — every dimension, measure, filter, allowed operator, aggregation, granularity, and enum value. Fully self-describing: it contains everything needed to build a valid query, including the state-like enum used for the default exclusion filter in execute_ads_query. Use to learn what fields exist and how to filter them before running a query. Prerequisite: get_ads_query_list (for valid level names). Next: optionally get_ads_query_materials, otherwise execute_ads_query. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_ads_query_schema(args: {
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // A valid level name from get_ads_query_list (e.g. profile, campaign, adgroup, target). Case-sensitive.
  level: string;
  // Platform key (same value used in get_ads_query_list).
  platform: string;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{ [key: string]: unknown; }>>; };
```

## 33. `get_ads_rule`

Helium 10 Get Ads Rule (Ads Rule, read). Returns the full detail of a single live automation rule (source=live, id=rule_id) or a single draft (source=draft, id=draft_id) as the generic rule document (each criteria carries its own conditions + action + lookback). Use it to review a rule before editing (manage_ads_rule draft_open mode=edit) or deleting (rule_delete). A live rule that carries uneditable_reason (e.g. UNSUPPORTED.RULE_HAS_TACTIC, meaning it uses a Tactic) CANNOT be edited through the MCP: do NOT open an edit draft for it -- direct the user to the Helium 10 Ads Rules & Automation page instead (delete is still allowed). source=live is tenant-scoped: pass profile_id, or omit it and the server auto-resolves the single accessible profile (returns a candidate list when several exist). Prerequisite: list_ads_rules to find the id. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_ads_rule(args: {
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // The rule_id (source=live) or draft_id (source=draft) to fetch. For source=live this is a 32-character lowercase hex rule_id (e.g. faa679c9b78e4b9fb8b452326f24dd00); for source=draft it is a decimal draft_id (e.g. 2089352922874187778).
  id: string;
  // Optional platform hint (e.g. amazon).
  platform?: string | null;
  // Target profile_id. Optional for source=draft; for source=live it scopes the tenant -- omit it only if you want the server to auto-resolve your single accessible profile.
  profile_id?: string | null;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // 'live' to fetch a committed rule by rule_id; 'draft' to fetch a draft by draft_id.
  source: string;
}): Promise<CallToolResult<{ [key: string]: unknown; }>>; };
```

## 34. `get_ads_rule_platforms`

Helium 10 Ads Rule Platforms (Ads Rule, step 1 of 4). Lists the advertising platforms and automation rule types available for this account, and for each rule type the supported actions and whether it is supported yet. The entry point for creating or editing an automation rule. Returns only names (no field catalog).

### Ads Rule pipeline

① get_ads_rule_platforms → ② get_ads_rule_schema(platform, rule_type) → ③ resolve profile_id / campaign ids via get_ads_query_materials → ④ manage_ads_rule (draft_open → draft_save → [confirm] → draft_commit).

v1 supports amazon + rule_type=BID only; other rule types report supported=false. Next: get_ads_rule_schema(platform, rule_type).

### Editing constraint

A rule that uses a Tactic CANNOT be edited through the MCP (draft_open mode=edit / draft_commit return UNSUPPORTED.RULE_HAS_TACTIC). Before planning an edit, check the rule's uneditable_reason from list_ads_rules / get_ads_rule; if set, direct the user to the Helium 10 Ads Rules & Automation page instead of building an edit. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_ads_rule_platforms(args: {
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{ [key: string]: unknown; }>>; };
```

## 35. `get_ads_rule_schema`

Helium 10 Ads Rule Schema (Ads Rule, step 2 of 4). Given a platform and rule_type (from get_ads_rule_platforms), returns the complete field catalog for that rule type: metrics, operators, actions, guardrails, lookback, frequency, timezone, targeting, constraints and dependencies. Always full (no section filter). Every enum value you put in a draft (metric / operator / action / frequency / timezone) MUST come from this return -- do not invent wire values -- and numbers are actual values per unitHints (e.g. ACOS 12% → 0.12, $0.50 → 0.50). Prerequisite: get_ads_rule_platforms. Next: get_ads_query_materials to resolve profile_id / campaign ids, then manage_ads_rule. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_ads_rule_schema(args: {
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Platform key from get_ads_rule_platforms (e.g. amazon). Case-sensitive.
  platform: string;
  // Rule type from get_ads_rule_platforms (e.g. BID). Case-sensitive.
  rule_type: string;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{ [key: string]: unknown; }>>; };
```

## 36. `get_amazon_ads_average_budget_utilization`

Amazon Average Budget Utilization (Helium 10 Ads). Returns, per campaign, how much of its budget was used on average across a date range, plus a total for the whole selection. AMAZON ONLY -- there is no platform argument, and no equivalent exists for Walmart or any other platform. Definition: total spend in the range divided by the sum of that range's per-day budgets -- a weighted ratio, NOT the average of the daily percentages, and the total is computed the same weighted way (so it will not equal the mean of the rows). Values are ratios, not percentages: 0.83 means 83% of budget used. A value above 1.0 means the campaign overspent its budget and is a real state, not an error. Use it for 'how fully did I spend my budget over the last N days', budget pacing reviews, and finding chronically under- or over-spending campaigns. Not for today's live consumption -- use get_amazon_ads_current_budget_utilization. Covers all four campaign types (sponsoredProducts, sponsoredBrands, sponsoredDisplay, sponsoredTV) and reads from daily report data, so the most recent hours of today are not reflected. A date range is required. Omit campaign_ids to cover every campaign of the profile; there is no paging, so check the returned truncated flag against total_count. Prerequisite: get_ads_query_materials(level=profile) for profile_id.

### Response shape

Returns {rows, total_average_budget_utilization, total_count, truncated}. Each row is {campaign_id, campaign_name, campaign_type, state, daily_budget, average_budget_utilization}. Read the overall figure from total_average_budget_utilization -- do NOT average the rows yourself, it is weighted and stays correct even when rows are truncated. campaign_id is a STRING, not a number: these ids exceed the range integers hold exactly, so compare and join them as strings. truncated=true means the row cap cut the list short and total_count tells you the real number of matching campaigns.

### Caveats worth surfacing to the user

Campaigns with no delivery in the range come back with 0 rather than being absent, so a long tail of zeros is normal and does not mean the query was wrong. Days a campaign had no delivery contribute no budget to the denominator, which pushes the ratio up for intermittently-paused campaigns. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_amazon_ads_average_budget_utilization(args: {
  // Optional numeric Amazon campaign ids. Omit to cover every campaign of the profile. An id that does not exist in this profile is not an error -- it simply does not appear in rows, so reconcile the result against what you asked for.
  campaign_ids?: Array<string> | null;
  // Optional campaign state filter. Only enabled, paused and archived are accepted; other states are rejected rather than silently ignored.
  campaign_status?: Array<string> | null;
  // Optional campaign type filter. Only sponsoredProducts, sponsoredBrands, sponsoredDisplay and sponsoredTV are accepted; other values are rejected rather than silently ignored.
  campaign_types?: Array<string> | null;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Range start, ISO YYYY-MM-DD (e.g. 2026-08-01). Note this differs from execute_ads_changelog_query on the same server, which takes DD-MM-YYYY.
  date_from: string;
  // Range end, ISO YYYY-MM-DD (e.g. 2026-08-19).
  date_to: string;
  // Target Amazon profile id from get_ads_query_materials(level=profile).
  profile_id: string;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{ [key: string]: unknown; }>>; };
```

## 37. `get_amazon_ads_current_budget_utilization`

Amazon Current Budget Utilization (Helium 10 Ads). Returns how much of TODAY's budget each of the given campaigns has consumed so far, read live from Amazon at call time. AMAZON ONLY -- there is no platform argument, and no equivalent exists for Walmart or any other platform. Values are ratios, not percentages, and can exceed 1.0: 0.95 means 95% of today's budget spent. Each row carries usage_updated_timestamp so you can tell a fresh reading from a stale one. Use it for 'which campaigns are about to run out of budget today', intraday pacing checks and budget top-up decisions. Not for historical pacing over a range -- use get_amazon_ads_average_budget_utilization. campaign_ids is REQUIRED (max 500 per call) and there is no date range, no paging and no total: Amazon's API answers per campaign id only. You do not pass campaign types; they are resolved server-side. Covers Sponsored Products, Brands and Display only -- Sponsored TV has no budget-usage API and comes back under unsupported. Prerequisite: get_ads_query_materials for profile_id and campaign ids.

### Response shape

Returns {rows, errors, unsupported} and nothing else -- no totals. Each row is {campaign_id, campaign_type, budget, current_budget_utilization, usage_updated_timestamp}; each errors entry is {campaign_id, code, details}; each unsupported entry is {campaign_id, campaign_type, reason}. campaign_id is a STRING, not a number: these ids exceed the range integers hold exactly.

### Reading the response

rows = live readings. errors = ids Amazon rejected or did not answer (code NO_RESPONSE). unsupported = ids that were never sent, either because they do not belong to this profile (reason 'campaign not found in this profile') or because their campaign type is outside SP / SB / SD (reason 'campaign type is not supported by Amazon's budget usage API') -- Sponsored TV in particular has no budget-usage API. Those two reasons mean different things: an unsupported type means switch to get_amazon_ads_average_budget_utilization, which does cover Sponsored TV; a not-found id means the id is wrong or belongs to another profile. EVERY id you pass lands in exactly one of the three arrays, so reconcile against your request. Report unsupported and errored ids to the user rather than treating them as zero. A reading of 0.0 is normal early in the advertiser's day -- check usage_updated_timestamp before calling it stale. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_amazon_ads_current_budget_utilization(args: {
  // Required. Numeric Amazon campaign ids to read, at most 500 per call. Amazon's budget-usage API has no 'all campaigns' mode, so resolve the ids first (e.g. get_ads_query_materials at campaign level, or the ids from a prior query).
  campaign_ids: Array<string>;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Target Amazon profile id from get_ads_query_materials(level=profile).
  profile_id: string;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{ [key: string]: unknown; }>>; };
```

## 38. `get_asin_bsr_history`

Helium 10 ASIN BSR history. Returns Best Sellers Rank history for one ASIN (asin): main-category constant-rank segments (start / end / bsr) plus per-subcategory daily points (date / bsr) when available. Default last 30 days when both dates omitted. Pass start_date and end_date together (both YYYY-MM-DD within the last 3 years), or omit both for the default last 30 days; do not pass only one date. Use when the user explicitly asks for BSR history or rank trend. Not for price (use get_asin_price_history), reviews/rating (use get_asin_reviews_history), keyword ranking (use Keyword Tracker), batch multi-ASIN, or discovery. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_asin_bsr_history(args: {
  // Required. ASIN to query, 10-char alphanumeric (e.g. B01LR5S6HK).
  asin: string;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Optional window end (inclusive), format yyyy-MM-dd (UTC day), no later than today. Pass it together with start_date, both within the last 3 years, or omit both dates for the default last 30 days. Do not pass only one date.
  end_date?: string | null;
  // Required. Amazon marketplace code. Allowed values: US, CA, MX, DE, ES, IT, FR, UK, IN, NL, AU, JP, AE, BR, SA.
  marketplace: "US" | "CA" | "MX" | "DE" | "ES" | "IT" | "FR" | "UK" | "IN" | "NL" | "AU" | "JP" | "AE" | "BR" | "SA";
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Optional window start (inclusive), format yyyy-MM-dd (UTC day). Pass it together with end_date, both within the last 3 years, or omit both dates for the default last 30 days. Do not pass only one date.
  start_date?: string | null;
}): Promise<CallToolResult<{
  // Success payload; full schema in the top-level data property.
  data: { [key: string]: unknown; };
  // No error on success.
  error: null;
} | {
  // No data when the tool fails.
  data: null;
  // Error payload; full schema in the top-level error property.
  error: { [key: string]: unknown; };
}>>; };
```

## 39. `get_asin_price_history`

Helium 10 ASIN price history. Returns price segments (start / end / price) for one ASIN (asin); identical values may merge. Default last 30 days when both dates omitted. Pass start_date and end_date together (both YYYY-MM-DD within the last 3 years), or omit both for the default last 30 days; do not pass only one date. Use when the user explicitly asks for price history or trend. Not for BSR (use get_asin_bsr_history), reviews/rating (use get_asin_reviews_history), search volume (use get_keywords_search_volume_history), sales history, batch multi-ASIN, or product discovery. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_asin_price_history(args: {
  // Required. ASIN to query, 10-char alphanumeric (e.g. B01LR5S6HK).
  asin: string;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Optional window end (inclusive), format yyyy-MM-dd (UTC day), no later than today. Pass it together with start_date, both within the last 3 years, or omit both dates for the default last 30 days. Do not pass only one date.
  end_date?: string | null;
  // Required. Amazon marketplace code. Allowed values: US, CA, MX, DE, ES, IT, FR, UK, IN, NL, AU, JP, AE, BR, SA.
  marketplace: "US" | "CA" | "MX" | "DE" | "ES" | "IT" | "FR" | "UK" | "IN" | "NL" | "AU" | "JP" | "AE" | "BR" | "SA";
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Optional window start (inclusive), format yyyy-MM-dd (UTC day). Pass it together with end_date, both within the last 3 years, or omit both dates for the default last 30 days. Do not pass only one date.
  start_date?: string | null;
}): Promise<CallToolResult<{
  // Success payload; full schema in the top-level data property.
  data: { [key: string]: unknown; };
  // No error on success.
  error: null;
} | {
  // No data when the tool fails.
  data: null;
  // Error payload; full schema in the top-level error property.
  error: { [key: string]: unknown; };
}>>; };
```

## 40. `get_asin_review_analysis`

Helium 10 ASIN Review Insights (HCE Review Insights Phase 1) via Amazon Customer Feedback. Given one marketplace and exactly one Amazon ASIN (child, parent, or standalone), returns positive/negative review topics sorted by mentions and by star-rating impact, plus topic trends (~6 months) for that ASIN. Use for product research, listing optimization, and competitor review-theme comparison. For multi-variation comparison, call get_listing_details then invoke this tool once per selected ASIN. Not for: category-level returns or browse-node analysis (use get_category_review_analysis); more than one ASIN per call (split calls; do not rely on silent truncation). Marketplace must be uppercase (e.g. US). Read-only. A successful call consumes one MCP quota unit even when topic payloads are empty/null after omit-null. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_asin_review_analysis(args: {
  // Amazon ASIN to analyze (child, parent, or standalone). Exactly ONE ASIN per call, exactly 10 alphanumeric characters. More than one ASIN is rejected with INVALID_INPUT.INVALID_ASIN_FORMAT — input is never silently truncated.
  asin: string;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Amazon marketplace code. Two-letter UPPERCASE code; supported values: US, UK, FR, IT, DE, ES, JP. Lowercase or unsupported codes are rejected with INVALID_INPUT.INVALID_MARKETPLACE.
  marketplace: "US" | "UK" | "FR" | "IT" | "DE" | "ES" | "JP";
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult>; };
```

## 41. `get_asin_reviews_history`

Helium 10 ASIN reviews history. Returns Review Count and Rating for one ASIN (asin) in one call, as daily points (date / review_count / rating). Default last 30 days when both dates omitted. Pass start_date and end_date together (both YYYY-MM-DD within the last 3 years), or omit both for the default last 30 days; do not pass only one date. Use when the user explicitly asks for reviews or rating history/trend. Not for price (use get_asin_price_history), BSR (use get_asin_bsr_history), search volume, batch multi-ASIN, or product discovery. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_asin_reviews_history(args: {
  // Required. ASIN to query, 10-char alphanumeric (e.g. B01LR5S6HK).
  asin: string;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Optional window end (inclusive), format yyyy-MM-dd (UTC day), no later than today. Pass it together with start_date, both within the last 3 years, or omit both dates for the default last 30 days. Do not pass only one date.
  end_date?: string | null;
  // Required. Amazon marketplace code. Allowed values: US, CA, MX, DE, ES, IT, FR, UK, IN, NL, AU, JP, AE, BR, SA.
  marketplace: "US" | "CA" | "MX" | "DE" | "ES" | "IT" | "FR" | "UK" | "IN" | "NL" | "AU" | "JP" | "AE" | "BR" | "SA";
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Optional window start (inclusive), format yyyy-MM-dd (UTC day). Pass it together with end_date, both within the last 3 years, or omit both dates for the default last 30 days. Do not pass only one date.
  start_date?: string | null;
}): Promise<CallToolResult<{
  // Success payload; full schema in the top-level data property.
  data: { [key: string]: unknown; };
  // No error on success.
  error: null;
} | {
  // No data when the tool fails.
  data: null;
  // Error payload; full schema in the top-level error property.
  error: { [key: string]: unknown; };
}>>; };
```

## 42. `get_bought_together_history`

Helium 10 Frequently Bought Together history. Returns raw daily observation records of the given product's "Frequently Bought Together" slot: each record is (event_date, product_id, bought_with), meaning the bought-with product was observed in this product's FBT slot on that date. IDs are marketplace-specific product IDs (ASIN on Amazon); the bought_with value can be passed directly to other tools' asin parameters. Daily snapshot data, not order counts. Default last 30 days when both dates omitted; pass start_date and end_date together (both within the last 3 years), do not pass only one date. Use when the user asks for "bought together", "FBT", bundle, or cross-sell partners of a product. Returns up to `limit` rows per call (default 100, max 10000); one page inlines at most ~500 rows / ~1MB, and requests far above that (>1000 rows or >~2MB) are delivered as a download link instead of inline rows. If data.pagination.has_more is true the result is incomplete: call again with `cursor` = data.pagination.next_cursor, but prefer narrowing filters over paginating through everything. Cursors expire 30 minutes after being issued and are bound to the query that issued them. Set response_format='download' when the user explicitly wants the data as a file. Not for: similar/competing listings (use search_competitors_by_asin); product targeting ads data (use search_product_targets_by_asin); product details like title/price (use get_listing_details per ASIN). This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_bought_together_history(args: {
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Opaque pagination cursor from a previous response's pagination.next_cursor. Omit for the first page. Pass it back verbatim with the same query arguments (limit may change between pages); never construct or edit it. Cursors are reusable but expire 30 minutes after being issued (pagination.cursor_expires_at) and are bound to the query that issued them.
  cursor?: string | null;
  // Optional window end (inclusive), format yyyy-MM-dd (UTC day), no later than today. Pass it together with start_date, both within the last 3 years, or omit both dates for the default last 30 days. Do not pass only one date.
  end_date?: string | null;
  // File format when results are delivered as a download (ignored for inline delivery). 'json' (default): gzip-compressed JSON, best for programmatic analysis. 'csv': UTF-8 CSV that opens directly in Excel/Sheets - prefer it when the user wants a spreadsheet/report file. Columns are the union of row fields; nested values stay JSON-encoded.
  export_format?: "json" | "csv";
  // Optional projection: return only these row fields, to save tokens when you need a few columns. Unknown names get a self-healing error listing the available fields. Overrides detail_level when both are set. Omit for the full field set.
  fields?: Array<string> | null;
  // Number of rows to return. Defaults to 100, max 10000; out-of-range values are clamped rather than rejected. Rows are large (~90B per row), so request only what the user needs — up to 500 rows per page come back inline (slightly larger asks are truncated and continue via `cursor`); requests far above the inline budget (>1000 rows or very large payloads) are delivered as a download link (response_format=auto). When pagination.has_more is true, fetch the next batch with `cursor`. Do NOT probe with limit=1 to learn the dataset size — every page carries the same total_count/has_more metadata, so ask for the rows you actually need and read the metadata of that response.
  limit?: number;
  // Amazon marketplace code, default US. Allowed values: US, CA, MX, BR, DE, ES, FR, IT, UK, NL, TR, IN, AE, EG, SA, SE, PL, JP, AU, SG, CN, BE, ZA, IE.
  marketplace?: "US" | "CA" | "MX" | "BR" | "DE" | "ES" | "FR" | "IT" | "UK" | "NL" | "TR" | "IN" | "AE" | "EG" | "SA" | "SE" | "PL" | "JP" | "AU" | "SG" | "CN" | "BE" | "ZA" | "IE";
  // Required. Marketplace-specific product ID to query — the ASIN on Amazon, 10-char alphanumeric (e.g. B01LR5S6HK).
  product_id: string;
  // How results are delivered. 'auto' (default): rows are returned inline when they fit the response budget, otherwise they are exported to a file and data.export.download_url is returned instead — so simply request the amount of data you need via `limit`. 'inline': always inline, oversized pages are truncated and continue via cursor. 'download': always a downloadable file, even for small results; the file contains exactly the window selected by `limit`/`cursor`, so size `limit` to what is wanted (the tool's max for the whole dataset). Use 'download' when the user wants a file or when you need many rows for analysis (fetch the link in your execution environment instead of paging through the conversation).
  response_format?: "auto" | "inline" | "download";
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Optional window start (inclusive), format yyyy-MM-dd (UTC day). Pass it together with end_date, both within the last 3 years, or omit both dates for the default last 30 days. Do not pass only one date.
  start_date?: string | null;
}): Promise<CallToolResult<{
  // Success payload; full schema in the top-level data property.
  data: { [key: string]: unknown; };
  // No error on success.
  error: null;
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
} | {
  // No data when the tool fails.
  data: null;
  // Error payload; full schema in the top-level error property.
  error: { [key: string]: unknown; };
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
}>>; };
```

## 43. `get_brand_seller_profiles`

Helium 10 helper — discovers and disambiguates a Brand-Registered Amazon seller × marketplace pair before an SQA call. Lists the caller's linked Brand-Registered sellers (non-Brand-Registered links are filtered out here, so downstream never sees them); each row is one seller_id with store_name and a marketplaces[] list (one card may span a region, e.g. NA = US/CA/MX). Drive it as a two-step picker: pick a row to lock seller_id, then pick one value from that row's marketplaces[] to lock marketplace, and pass both to get_search_query_performance. Use when the user references 'my store / my account / this listing' without naming a pair, or when one seller spans multiple marketplaces. Not for: a pair already provided or locked this session (skip it); adding/editing seller links (Helium 10 account settings); seller-level sales/fee/inventory analytics. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_brand_seller_profiles(args: {
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Optional marketplace filter (US, UK, DE, JP, CA, FR, IT, ES). When set, only seller rows whose marketplaces[] contains this value are returned. Does not trim marketplaces[] on each row.
  marketplace?: "US" | "UK" | "DE" | "JP" | "CA" | "FR" | "IT" | "ES" | null;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{
  // Non-null on success and on empty-set soft errors (sellers may be []). Null on hard errors.
  data: {
  _meta?: {
  // True when the server capped results at 200 rows.
  truncated?: boolean;
};
  // Sorted by store_name ascending. Empty on soft empty-set errors.
  sellers: Array<{
  // Marketplaces where this seller_id is visible; agent must pick one for downstream calls.
  marketplaces: Array<"US" | "UK" | "DE" | "JP" | "CA" | "FR" | "IT" | "ES">;
  // Amazon Seller Central ID; pass directly to get_search_query_performance.seller_id.
  seller_id: string;
  // Display name on the H10 Amazon Seller binding card (picker label only).
  store_name: string;
}>;
  summary: {
  // Number of seller rows returned.
  total_sellers: number;
};
} | null;
  // Present on INTEGRATION.SELLER_ACCOUNT_NOT_CONNECTED or INTEGRATION.SELLER_NOT_ELIGIBLE.
  error?: {
  // get_brand_seller_profiles returns data on success only; every precondition failure arrives with isError=true and a two-segment code (Helium 10 MCP Error Code specification): INTEGRATION.SELLER_ACCOUNT_NOT_CONNECTED (no linked seller), INTEGRATION.SELLER_NOT_ELIGIBLE (no Brand-Registered seller), INVALID_INPUT.INVALID_ENUM (bad marketplace filter).
  code: string;
  details?: { [key: string]: unknown; };
  // Short, user-friendly, actionable.
  message: string;
  // Optional human-readable remediation guidance from MCP error normalization.
  remediation?: string;
};
}>>; };
```

## 44. `get_buybox_summary`

Helium 10 Alerts — current Buy Box standing across a brand's monitored ASINs. Omit marketplace for an all-marketplaces overview (winning/losing counts and win rate per marketplace); pass marketplace for single-market detail including the specific losing ASINs, their current Buy Box winner, and ASINs lost in the last 7 days. ASINs with no Buy Box winner data at all (neutral — not yet synced or no competing offers) are excluded from every losing_count, but still count toward win_rate's total-monitored denominator. For one ASIN's Buy Box history over time use search_buybox_history. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_buybox_summary(args: {
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Optional marketplace code. Omit for an all-marketplaces overview; pass US/UK/DE/JP/etc. for single-market detail.
  marketplace?: "US" | "CA" | "MX" | "DE" | "ES" | "IT" | "FR" | "UK" | "IN" | "NL" | "AU" | "JP" | "AE" | "BR" | "SA" | null;
  // Helium 10 seller identifier.
  seller_id: string;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{
  // Buy Box summary. Overview mode (no marketplace) populates total_* and by_marketplace; single-marketplace mode populates marketplace, *_count, losing_asins and recently_lost. Null on error.
  data: {
  // ASINs not holding the Buy Box (excludes neutral ASINs with no Buy Box winner data).
  buybox_losing_count?: number | null;
  // ASINs holding the Buy Box.
  buybox_winning_count?: number | null;
  // Per-marketplace Buy Box rollup (overview mode).
  by_marketplace?: Array<{
  // ASINs not holding the Buy Box (excludes neutral ASINs with no Buy Box winner data).
  losing_count?: number | null;
  // Marketplace code, e.g. US.
  marketplace?: string | null;
  // ASINs lost in the last 7 days.
  recently_lost_count?: number | null;
  // Win rate 0.0–1.0.
  win_rate?: number | null;
  // ASINs holding the Buy Box.
  winning_count?: number | null;
}> | null;
  // ASINs not holding the Buy Box (single-marketplace mode).
  losing_asins?: Array<{
  // Amazon ASIN that is not holding the Buy Box.
  asin?: string | null;
  // Date the Buy Box standing last changed, format YYYY-MM-DD.
  change_since?: string | null;
  // Seller ID currently holding the Buy Box.
  current_winner?: string | null;
}> | null;
  // Queried marketplace code (single-marketplace mode).
  marketplace?: string | null;
  // Overall Buy Box win rate 0.0–1.0.
  overall_win_rate?: number | null;
  // ASINs lost in the last 7 days (single-marketplace mode).
  recently_lost?: Array<{
  // Amazon ASIN that is not holding the Buy Box.
  asin?: string | null;
  // Date the Buy Box standing last changed, format YYYY-MM-DD.
  change_since?: string | null;
  // Seller ID currently holding the Buy Box.
  current_winner?: string | null;
}> | null;
  // ASINs not holding the Buy Box across all marketplaces (excludes neutral ASINs with no Buy Box winner data).
  total_losing?: number | null;
  // Number of marketplaces with monitored ASINs.
  total_marketplaces?: number | null;
  // Monitored ASINs in the marketplace.
  total_monitored_asins?: number | null;
  // ASINs holding the Buy Box across all marketplaces.
  total_winning?: number | null;
} | null;
  error: null;
} | {
  data: null;
  // Structured error response from io-api.
  error: {
  // MCP string error code.
  code: string;
  // Optional structured error details.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Optional human-readable remediation guidance (may be omitted).
  remediation?: string;
};
}>>; };
```

## 45. `get_category_review_analysis`

Helium 10 Category Review Insights (HCE Review Insights Phase 2, including Returns) via Amazon Customer Feedback. Given one marketplace and exactly one confirmed browse_node_id, returns category review topics (by mentions and by star-rating impact), review trends, and return topics/trends (~6 months). Use for category feedback research, return-risk checks, and category trend monitoring. Requires a confirmed browse node id—not a fuzzy category name. If the user only has a phrase, call search_browse_nodes and confirm one id first—never pass unresolved candidate sets. Not for: ASIN-level returns; ASIN-level review topics (use get_asin_review_analysis); more than one browse node per call. Marketplace must be uppercase (e.g. US). Read-only. A successful call consumes one MCP quota unit even when payloads are empty after omit-null. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_category_review_analysis(args: {
  // Confirmed numeric Amazon browse node id (from search_browse_nodes after user confirmation), e.g. "3733721". Exactly ONE browse node id per call; multiple ids, category names, or any other non-numeric value are rejected with INVALID_INPUT.INVALID_BROWSE_NODE — input is never silently truncated.
  browse_node_id: string;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Amazon marketplace code. Two-letter UPPERCASE code; supported values: US, UK, FR, IT, DE, ES, JP. Lowercase or unsupported codes are rejected with INVALID_INPUT.INVALID_MARKETPLACE.
  marketplace: "US" | "UK" | "FR" | "IT" | "DE" | "ES" | "JP";
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult>; };
```

## 46. `get_change_rollup`

Helium 10 Alerts — account-level rollup of what changed in the last 24h or 7d for a seller in one marketplace. Aggregates alert events (plus Seller Feedback notifications) by urgency (P0–P3), by event type, and by ASIN, and returns the P0 critical events that need immediate action. Use to answer 'what changed today / this week?' and 'what's most urgent?'. For raw per-event detail use search_alert_events. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_change_rollup(args: {
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Marketplace code: US, UK, DE, JP, etc.
  marketplace: "US" | "CA" | "MX" | "DE" | "ES" | "IT" | "FR" | "UK" | "IN" | "NL" | "AU" | "JP" | "AE" | "BR" | "SA";
  // Helium 10 seller identifier.
  seller_id: string;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Aggregation window: '24h' (default) or '7d'.
  window?: "24h" | "7d";
}): Promise<CallToolResult<{
  // Change rollup payload. Null on error.
  data: {
  // Counts grouped by ASIN (Seller Feedback is seller-level and excluded here).
  by_asin?: Array<{
  // Amazon ASIN.
  asin?: string | null;
  // Number of events for this ASIN.
  event_count?: number | null;
  // Most-urgent level for this ASIN (P0–P3).
  highest_urgency?: string | null;
}> | null;
  // Counts grouped by event type (incl. feedback-negative/neutral/positive).
  by_type?: Array<{
  // Event type code; null for Seller Feedback notifications.
  alert_type?: number | null;
  // Human-readable type label.
  alert_type_info?: string | null;
  // Number of events of this type.
  count?: number | null;
}> | null;
  // Event counts by urgency level.
  by_urgency?: {
  // P0 (urgent) count.
  p0?: number | null;
  // P1 (high) count.
  p1?: number | null;
  // P2 (medium) count.
  p2?: number | null;
  // P3 (low/positive) count.
  p3?: number | null;
} | null;
  // P0 event details requiring immediate attention.
  critical_events?: Array<{
  // Alert type code (integer 1–33), e.g. 7 = Price changed.
  alert_type?: number | null;
  // Human-readable type label.
  alert_type_info?: string | null;
  // Amazon ASIN.
  asin?: string | null;
  // Unix timestamp (seconds, UTC) when the event fired.
  fired_at?: number | null;
  // Value after the change.
  new_value?: string | null;
  // Value before the change.
  old_value?: string | null;
}> | null;
  // Total change events (incl. Seller Feedback) in the window.
  total_events?: number | null;
  // Aggregation window: '24h' or '7d'.
  window?: string | null;
} | null;
  error: null;
} | {
  data: null;
  // Structured error response from io-api.
  error: {
  // MCP string error code.
  code: string;
  // Optional structured error details.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Optional human-readable remediation guidance (may be omitted).
  remediation?: string;
};
}>>; };
```

## 47. `get_competitor_overview`

Helium 10 Competitor Benchmark. For an ASIN group (main + up to 10 competitors), returns competitive benchmark data across 6 metrics — sales, revenue, price, BSR, review count, rating — for every ASIN in the session alongside two group averages: searched_products_avg (your session competitors) and top_competitors_avg (top 5 market performers, auto-resolved by LA). All ASINs returned in one call; the agent pairs each ASIN's values with the group averages to answer comparison questions. Omit competitor_asins to rely on top_competitors_avg only. Use for competitive health checks, pre-launch positioning, and finding the biggest performance gaps. Not for: full attribute comparison (use compare_listings); keyword-level analysis (use get_top_keywords). This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_competitor_overview(args: {
  // Alias for ``main_asin`` (accepted because callers often send ``asin``). Prefer ``main_asin``; when both are supplied ``main_asin`` wins. Leave unset if you already pass ``main_asin``.
  asin?: string | null;
  // Optional list of up to 10 competitor ASINs (each 10 characters). Omit (or pass an empty list) to let upstream auto-discover competitors and return their group average only (no per-competitor rows); supply explicit ASINs to instead receive per-ASIN sales blocks plus the cross-ASIN seed average.
  competitor_asins?: Array<string> | null;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Main Amazon ASIN to benchmark (10 characters). Always the anchor of the competitor comparison. Required — the parameter is named ``main_asin`` (not ``asin``); you may also pass it via the ``asin`` alias.
  main_asin?: string | null;
  // Optional marketplace short code where the ASIN is listed. Supported: US, CA, MX, DE, ES, IT, FR, UK, IN, NL, AE, BR, AU. Defaults to US when omitted. Case-insensitive; whitespace is trimmed.
  marketplace?: string;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{
  // Side-by-side competitive comparison across 6 metrics: sales, revenue, price, BSR, review count, rating. Single mode reports the group average across auto-discovered competitors (``avg_competitors_sales_data``); multi mode reports the cross-ASIN seed average (``searched_products_avg``) plus per-ASIN rows (``asin_sales_data``). Both modes also surface ``main_asin_sales_data`` — a 6-field projection of the main ASIN's sales data (monthly sales / monthly revenue / price / sales rank / review count / reviews rating) — so the LLM can compare the main listing against the competitor aggregates without re-reading ``get_listing_details``.
  data: {
  // Multi-mode only — per-ASIN sales blocks keyed by ASIN (main ASIN plus every caller-supplied competitor). Each entry carries two six-key rows: ``sales_data`` (this ASIN's own listing row) and ``avg_competitors_sales_data`` (the per-ASIN 'market' average column). Both are limited to the same six headline metric keys as ``searched_products_avg``. Null in single mode.
  asin_sales_data?: { [key: string]: {
  // Per-ASIN 'market' average column (six headline metric keys).
  avg_competitors_sales_data?: {
  // Parent Level Revenue — Estimated revenue over the past 30 days (major currency units; cross-ASIN mean on ``avg_*`` rows).
  monthly_revenue?: number | null;
  // Parent Level Sales — Estimated units sold over the past 30 days (cross-ASIN mean on ``avg_*`` rows).
  monthly_sales?: number | null;
  // Price — Product selling price (major currency units; ``-1`` sentinel collapsed to null on seed rows; cross-ASIN mean on ``avg_*`` rows).
  price?: number | null;
  // Review — Total number of customer reviews (cross-ASIN mean on ``avg_*`` rows).
  review_count?: number | null;
  // Rating — Amazon customer reviews rating from poor (1.0) to outstanding (5.0) (cross-ASIN mean on ``avg_*`` rows).
  reviews_rating?: number | null;
  // Category BSR — Best Seller Rank for the main category (cross-ASIN mean on ``avg_*`` rows).
  sales_rank?: number | null;
} | null;
  // This ASIN's own listing row (six headline metric keys).
  sales_data?: {
  // Parent Level Revenue — Estimated revenue over the past 30 days (major currency units; cross-ASIN mean on ``avg_*`` rows).
  monthly_revenue?: number | null;
  // Parent Level Sales — Estimated units sold over the past 30 days (cross-ASIN mean on ``avg_*`` rows).
  monthly_sales?: number | null;
  // Price — Product selling price (major currency units; ``-1`` sentinel collapsed to null on seed rows; cross-ASIN mean on ``avg_*`` rows).
  price?: number | null;
  // Review — Total number of customer reviews (cross-ASIN mean on ``avg_*`` rows).
  review_count?: number | null;
  // Rating — Amazon customer reviews rating from poor (1.0) to outstanding (5.0) (cross-ASIN mean on ``avg_*`` rows).
  reviews_rating?: number | null;
  // Category BSR — Best Seller Rank for the main category (cross-ASIN mean on ``avg_*`` rows).
  sales_rank?: number | null;
} | null;
}; } | null;
  // Single-mode only — group average across the auto-discovered competitors (excluding the main ASIN). Sales / revenue / price / BSR / review count / rating averages. Null in multi mode.
  avg_competitors_sales_data?: {
  // Average ASIN-level monthly revenue (major currency units).
  child_monthly_revenue?: number | null;
  // Average ASIN-level monthly sales (units).
  child_monthly_sales?: number | null;
  // Average parent-level monthly revenue (major currency units).
  monthly_revenue?: number | null;
  // Average parent-level monthly sales (units).
  monthly_sales?: number | null;
  // Average Price across the participating ASINs (major currency units, rounded).
  price?: number | null;
  // Average review count across participating ASINs.
  review_count?: number | null;
  // Average rating across participating ASINs.
  reviews_rating?: number | null;
  // Average BSR across the participating ASINs.
  sales_rank?: number | null;
} | null;
  // Number of competitors that participated in the average computation.
  competitor_count?: number | null;
  // ``auto`` for single mode (upstream auto-discovers competitors), ``manual`` for multi mode (caller supplies competitor ASINs).
  competitor_source?: string | null;
  // 6-field projection of the main ASIN's sales data, surfaced inside ``get_competitor_overview`` so the LLM can line the main listing up against ``avg_competitors_sales_data`` (single mode) or ``searched_products_avg`` / ``asin_sales_data`` (multi mode) without having to re-read the ``get_listing_details`` section. Six fields: monthly_sales / monthly_revenue / price / sales_rank / review_count / reviews_rating. Values mirror the main ASIN's ``get_listing_details.seed_data`` (``price`` and ``monthly_revenue`` in major currency units, the ``-1`` 'unknown' sentinel collapsed to null). Populated in both single and multi modes; omitted (key absent) when no seed data is available.
  main_asin_sales_data?: {
  // Revenue — Estimated revenue for the product over the past 30 days (major currency units). Mirrors ``get_listing_details.seed_data.monthly_revenue`` for the main ASIN.
  monthly_revenue?: number | null;
  // Sales — Estimated number of units sold over the past 30 days. Mirrors ``get_listing_details.seed_data.monthly_sales`` for the main ASIN.
  monthly_sales?: number | null;
  // Price — Price at which the product is sold (major currency units; ``-1`` sentinel collapsed to null). Mirrors ``get_listing_details.seed_data.price`` for the main ASIN.
  price?: number | null;
  // Number of Reviews — Total number of customer reviews. Mirrors ``get_listing_details.seed_data.review_count`` for the main ASIN.
  review_count?: number | null;
  // Rating — Amazon customer reviews rating from poor (1.0) to outstanding (5.0). Mirrors ``get_listing_details.seed_data.reviews_rating`` for the main ASIN.
  reviews_rating?: number | null;
  // Category BSR — Best Seller Rank; Amazon-awarded rank for the main category. Mirrors ``get_listing_details.seed_data.sales_rank`` for the main ASIN.
  sales_rank?: number | null;
} | null;
  // Multi-mode only — cross-ASIN average across the seed group (main + caller-supplied competitors), limited to six headline metric keys (monthly_sales / monthly_revenue / price / sales_rank / review_count / reviews_rating). Null in single mode.
  searched_products_avg?: {
  // Parent Level Revenue — Estimated revenue over the past 30 days (major currency units; cross-ASIN mean on ``avg_*`` rows).
  monthly_revenue?: number | null;
  // Parent Level Sales — Estimated units sold over the past 30 days (cross-ASIN mean on ``avg_*`` rows).
  monthly_sales?: number | null;
  // Price — Product selling price (major currency units; ``-1`` sentinel collapsed to null on seed rows; cross-ASIN mean on ``avg_*`` rows).
  price?: number | null;
  // Review — Total number of customer reviews (cross-ASIN mean on ``avg_*`` rows).
  review_count?: number | null;
  // Rating — Amazon customer reviews rating from poor (1.0) to outstanding (5.0) (cross-ASIN mean on ``avg_*`` rows).
  reviews_rating?: number | null;
  // Category BSR — Best Seller Rank for the main category (cross-ASIN mean on ``avg_*`` rows).
  sales_rank?: number | null;
} | null;
};
  // Always null — Listing Analyzer surfaces failures as an MCP error result (isError=true), not as an inline error payload.
  error: null;
}>>; };
```

## 48. `get_fba_inventory_health_status`

Return FBA inventory health metrics per product row: days of supply (a decimal number of days, e.g. 12.75 — report it as-is, do not present it as a whole number) and inventory age bucket unit counts (<31 days, 31-180 days, >180 days). Each row includes standard product information (without fulfillment_type — implicit FBA). Not on-hand/inbound quantity counts; not FBM; not a time series. The unit counts here are health signals only — use them to judge aging/slow-moving risk, never as an authoritative quantity source. Do NOT compare or reconcile them with get_inventory_values; the two use different methodologies and are not expected to match. Use when the user asks about FBA stock health, days of supply, slow-moving or aged inventory in Amazon fulfillment centers, or restock risk from an FBA aging perspective. Not for: quantity snapshots (available, inbound, unfulfillable) or FBM (use get_inventory_values); sales velocity over time (use get_sales_velocity); profit & loss or fees (use Phase 1 P&L tools). Combine seller_ids, marketplace, asins, skus, and pagination to limit payload size. Do not pass fulfillment_type — this tool does not accept it. Pass days_of_supply_min / days_of_supply_max to filter by supply days server-side (inclusive bounds); rows with null days_of_supply are omitted when a range filter is active — do not assume they were evaluated as zero. Pass inv_age_stage (less_31_days | 31_to_180_days | over_180_days) to find SKUs that HAVE units in that age bucket: this is a presence filter, so returned rows still show all three inv_age_* counts and a SKU with over-180-day stock may also have younger inventory. Combine inv_age_stage with days_of_supply_max for restock / long-term-storage triage. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_fba_inventory_health_status(args: {
  // Optional. SQL IN filter by ASIN list (array of strings, e.g. ['B001234567']). Default null (all ASINs).
  asins?: Array<string> | null;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Optional. Maximum inclusive health.days_of_supply. The bound itself must be a non-negative integer, but it is compared against a fractional value: only rows where days_of_supply <= days_of_supply_max are returned, so days_of_supply_max=14 keeps 14.0 and 13.9 but drops 14.5. Rows with null days_of_supply are excluded when this or days_of_supply_min is set. May be combined with days_of_supply_min.
  days_of_supply_max?: number | null;
  // Optional. Minimum inclusive health.days_of_supply. The bound itself must be a non-negative integer, but it is compared against a fractional value: only rows where days_of_supply >= days_of_supply_min are returned, so days_of_supply_min=14 keeps 14.0 and 14.5 but drops 13.9. Rows with null days_of_supply are excluded when this or days_of_supply_max is set. May be combined with days_of_supply_max.
  days_of_supply_min?: number | null;
  // Optional. Inventory age bucket presence filter. Returns only rows that HAVE units (count > 0) in the selected bucket. Allowed values: less_31_days, 31_to_180_days, over_180_days. At most one value (mutually exclusive); omit for no age-stage filter. This is a presence filter, not exclusivity: a matching row still reports all three inv_age_* counts, so a SKU matching over_180_days may also have younger inventory.
  inv_age_stage?: "less_31_days" | "31_to_180_days" | "over_180_days" | null;
  // Optional marketplace filter as an array of region short codes ONLY (e.g. ['US', 'CA', 'MX']); Amazon SP-API marketplace IDs (e.g. 'ATVPDKIKX0DER'), seller IDs, and domains are rejected. Allowed: AE, AU, BE, BR, CA, CN, DE, EG, ES, FR, IE, IN, IT, JP, MX, NL, PL, SA, SE, SG, TR, UK, US, ZA. Default null (all authorised marketplaces).
  marketplace?: Array<string> | null;
  // Optional. Page number (integer), starts from 1. Default 1.
  page_index?: number | null;
  // Optional. Rows per page (integer). Min 5, max 1000. Default 50.
  page_size?: number | null;
  // Optional. SQL IN filter on seller account IDs (array of strings). Profits intersects with sellers from Pacvue token; no intersection → empty result. Default null (all authorised sellers).
  seller_ids?: Array<string> | null;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Optional. SQL IN filter by seller SKU list (array of strings). Default null (all SKUs).
  skus?: Array<string> | null;
}): Promise<CallToolResult<{
  // Paginated FBA inventory health status. Null when no matching data exists.
  data: {
  // 1-based page number that was returned. Matches the page_index request parameter (defaults to 1 when not specified).
  page_index: number;
  // Maximum number of rows per page that was applied. Matches the page_size request parameter (defaults to 50 when not specified; range 5–1000).
  page_size: number;
  // FBA product rows for the current page, each containing product identity (asin, sku, product_name, brand, image_url, marketplace, seller_id) and a health metrics object. No FBM rows. Empty list when no FBA products match the filters.
  rows: Array<{
  // Amazon Standard Identification Number (ASIN) of the child product variant, e.g. 'B001234567'. Null when the product level is parent_asin.
  asin?: string | null;
  // Brand name associated with the product as registered in Amazon Brand Registry or the seller's catalog. Null when not available.
  brand?: string | null;
  // FBA inventory health metrics: days of supply and inventory age buckets. All four keys are present on every row.
  health: {
  // Estimated days current FBA inventory will last at recent sell-through rate (float, 2 dp — fractional values such as 12.75 are normal; do not assume a whole number of days). Null when the backend cannot compute supply days. Note: when a days_of_supply range filter is active, null rows are excluded from results (HP-1363).
  days_of_supply?: number | null;
  // Units aged 31-180 days in FBA.
  inv_age_31_to_180_days: number;
  // Units with inventory age less than 31 days in FBA.
  inv_age_less_31_days: number;
  // Units aged over 180 days in FBA.
  inv_age_over_180_days: number;
};
  // HTTPS URL of the main product image thumbnail served by Amazon. Null when the product has no image or metadata is unavailable.
  image_url?: string | null;
  // Amazon marketplace short country code where this product is sold (e.g. 'US', 'CA', 'UK'). Same vocabulary as the 'marketplace' filter parameter — this value can be fed straight back as a filter.
  marketplace?: string | null;
  // Parent ASIN that groups child variants (e.g. different sizes/colours). Populated at all product levels; null when not applicable.
  parent_asin?: string | null;
  // Amazon product listing title as it appears on the detail page. May be null if the product metadata is not available.
  product_name?: string | null;
  // Amazon seller account ID (merchant token) that owns this product listing, e.g. 'A2EUQ1WTGCTBG2'. Corresponds to the seller_ids filter parameter.
  seller_id?: string | null;
  // Seller-defined SKU, unique within a seller account. Reliably populated when product_level=sku. At asin/parent_asin level: typically null in non-series tools, but may be non-null in *_series tools; use asin/parent_asin for identity matching.
  sku?: string | null;
}>;
  // Total number of matching product rows across all pages for the given filters. Use this value together with page_size to determine the total number of pages: ceil(total_count / page_size).
  total_count: number;
} | null;
  // Null on success; error object on failure.
  error?: {
  // Error code string, e.g. 'INVALID_INPUT.DATE_RANGE_INVALID'.
  code: string;
  // Additional error context keyed by field or topic.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Suggested action to resolve the error.
  remediation: string;
} | null;
  // Optional non-blocking advisory (spec §6.1). Present only when the platform attaches one — currently a quota near-limit reminder (code QUOTA_NEAR_LIMIT). Null/absent on the vast majority of responses. Independent of 'error': the call still succeeded.
  notice?: {
  // Notice code, e.g. 'QUOTA_NEAR_LIMIT'.
  code: string;
  // Human-readable advisory message for the end user.
  message: string;
  // Advisory severity, e.g. 'warning'.
  severity: string;
} | null;
}>>; };
```

## 49. `get_inventory_values`

Return current Amazon inventory quantity metrics per product row, with fulfillment_type (FBA or FBM) on each row. FBA rows include available, inbound pipeline quantities, and unfulfillable. FBM rows include available only (FBA-only keys are omitted, not zero-filled). Not a time series; not inventory age or days-of-supply. This is the authoritative source for inventory quantities; do NOT reconcile or cross-check these counts against get_fba_inventory_health_status, whose numbers are health signals only and follow a different methodology. Use when the user asks how much stock is on hand, in transit to Amazon (inbound), or unfulfillable; needs SKU/ASIN-level inventory counts across sellers/marketplaces; restock or fulfillment planning based on quantity snapshots. Not for: FBA health/aging/days of supply (use get_fba_inventory_health_status); sales velocity or units sold over time (use get_sales_velocity); profit & loss or fees (use Phase 1 P&L tools). Pass seller_ids, marketplace, asins, skus, and/or fulfillment_type (FBA | FBM) to limit rows; omit fulfillment_type for both channels. Use pagination when listing many SKUs. Pass available_min / available_max to filter rows by on-hand quantity (inclusive bounds on inventory.available) without post-processing in agent context. Combine sort_by=available with a small page_size to retrieve top/bottom N SKUs by stock level in one call: sort_order=asc surfaces lowest-stock SKUs first (restock prioritization), sort_order=desc surfaces highest-stock SKUs first. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_inventory_values(args: {
  // Optional. SQL IN filter by ASIN list (array of strings, e.g. ['B001234567']). Default null (all ASINs).
  asins?: Array<string> | null;
  // Optional. Maximum inclusive inventory.available quantity (non-negative integer). When set, only rows where available <= available_max are returned. May be combined with available_min.
  available_max?: number | null;
  // Optional. Minimum inclusive inventory.available quantity (non-negative integer). When set, only rows where available >= available_min are returned. May be combined with available_max.
  available_min?: number | null;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Optional fulfillment channel filter: FBA | FBM. Omit to query both FBA and FBM inventory.
  fulfillment_type?: "FBA" | "FBM" | null;
  // Optional marketplace filter as an array of region short codes ONLY (e.g. ['US', 'CA', 'MX']); Amazon SP-API marketplace IDs (e.g. 'ATVPDKIKX0DER'), seller IDs, and domains are rejected. Allowed: AE, AU, BE, BR, CA, CN, DE, EG, ES, FR, IE, IN, IT, JP, MX, NL, PL, SA, SE, SG, TR, UK, US, ZA. Default null (all authorised marketplaces).
  marketplace?: Array<string> | null;
  // Optional. Page number (integer), starts from 1. Default 1.
  page_index?: number | null;
  // Optional. Rows per page (integer). Min 5, max 1000. Default 50.
  page_size?: number | null;
  // Optional. SQL IN filter on seller account IDs (array of strings). Profits intersects with sellers from Pacvue token; no intersection → empty result. Default null (all authorised sellers).
  seller_ids?: Array<string> | null;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Optional. SQL IN filter by seller SKU list (array of strings). Default null (all SKUs).
  skus?: Array<string> | null;
  // Optional. Metric to sort product rows by, using each row's inventory.available value. If omitted, server default order applies (unspecified, stable). Allowed values: available.
  sort_by?: "available" | null;
  // Optional. Sort direction for sort_by. 'desc' = highest value first (default); 'asc' = lowest value first. Ignored if sort_by is not provided.
  sort_order?: "asc" | "desc" | null;
}): Promise<CallToolResult<{
  // Paginated inventory values (FBA and/or FBM). Null when no matching data exists.
  data: {
  // 1-based page number that was returned. Matches the page_index request parameter (defaults to 1 when not specified).
  page_index: number;
  // Maximum number of rows per page that was applied. Matches the page_size request parameter (defaults to 50 when not specified; range 5–1000).
  page_size: number;
  // Product rows for the current page, each containing product identity (asin, sku, product_name, brand, image_url, marketplace, seller_id, fulfillment_type) and an inventory metrics object. Empty list when no products match the filters.
  rows: Array<{
  // Amazon Standard Identification Number (ASIN) of the child product variant, e.g. 'B001234567'. Null when the product level is parent_asin.
  asin?: string | null;
  // Brand name associated with the product as registered in Amazon Brand Registry or the seller's catalog. Null when not available.
  brand?: string | null;
  // Fulfillment channel for this row: 'FBA' (Fulfilled by Amazon) or 'FBM' (Fulfilled by Merchant). Determines which inventory metrics are present on the row.
  fulfillment_type?: string | null;
  // HTTPS URL of the main product image thumbnail served by Amazon. Null when the product has no image or metadata is unavailable.
  image_url?: string | null;
  // Inventory quantity metrics. FBA rows include all 6 fields (available, inbound_quantity, inbound_working, inbound_shipped, inbound_received, unfulfillable). FBM rows include only 'available'; FBA-only keys are omitted.
  inventory: {
  // Non-negative sellable/on-hand quantity for the fulfillment channel. Present on both FBA and FBM rows.
  available: number;
  // Total units in the FBA inbound pipeline (working + shipped + received). FBA rows only; omitted on FBM rows.
  inbound_quantity?: number | null;
  // Units received at Amazon fulfillment center but not yet available. FBA rows only; omitted on FBM rows.
  inbound_received?: number | null;
  // Units in inbound shipments in transit to Amazon. FBA rows only; omitted on FBM rows.
  inbound_shipped?: number | null;
  // Units in inbound shipments being prepared. FBA rows only; omitted on FBM rows.
  inbound_working?: number | null;
  // Units at Amazon that are defective, damaged, or otherwise unsellable. FBA rows only; omitted on FBM rows.
  unfulfillable?: number | null;
};
  // Amazon marketplace short country code where this product is sold (e.g. 'US', 'CA', 'UK'). Same vocabulary as the 'marketplace' filter parameter — this value can be fed straight back as a filter.
  marketplace?: string | null;
  // Parent ASIN that groups child variants (e.g. different sizes/colours). Populated at all product levels; null when not applicable.
  parent_asin?: string | null;
  // Amazon product listing title as it appears on the detail page. May be null if the product metadata is not available.
  product_name?: string | null;
  // Amazon seller account ID (merchant token) that owns this product listing, e.g. 'A2EUQ1WTGCTBG2'. Corresponds to the seller_ids filter parameter.
  seller_id?: string | null;
  // Seller-defined SKU, unique within a seller account. Reliably populated when product_level=sku. At asin/parent_asin level: typically null in non-series tools, but may be non-null in *_series tools; use asin/parent_asin for identity matching.
  sku?: string | null;
}>;
  // Total number of matching product rows across all pages for the given filters. Use this value together with page_size to determine the total number of pages: ceil(total_count / page_size).
  total_count: number;
} | null;
  // Null on success; error object on failure.
  error?: {
  // Error code string, e.g. 'INVALID_INPUT.DATE_RANGE_INVALID'.
  code: string;
  // Additional error context keyed by field or topic.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Suggested action to resolve the error.
  remediation: string;
} | null;
  // Optional non-blocking advisory (spec §6.1). Present only when the platform attaches one — currently a quota near-limit reminder (code QUOTA_NEAR_LIMIT). Null/absent on the vast majority of responses. Independent of 'error': the call still succeeded.
  notice?: {
  // Notice code, e.g. 'QUOTA_NEAR_LIMIT'.
  code: string;
  // Human-readable advisory message for the end user.
  message: string;
  // Advisory severity, e.g. 'warning'.
  severity: string;
} | null;
}>>; };
```

## 50. `get_keyword_performance_with_competitors`

Helium 10 Listing Builder — scores how well a keyword set is written into listing copy for your ASIN and up to 10 competitors: a per-ASIN Keyword Performance Score (KPS) by field plus coverage counts, and per keyword the text-match types each ASIN achieves in title / bullets / description, plus demand metrics and organic rank. Competitors are always scored on their published listing; so is your ASIN unless you pass `main_listing_draft` for unpublished copy. Use to find keywords competitors wrote into their copy and you did not, and which field each belongs in. Reading `text_match`: Codes, strongest first: E = Exact Phrase; P = Plural/Singular; R = Partial Phrase; F = Field Broad (all words in that field); L = Listing Broad (words spread across fields). Not for: benchmarking listings on sales, reviews, price or quality score (use compare_listings); keyword gaps derived from organic search-result data rather than from listing copy (use compare_asin_keywords). This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_keyword_performance_with_competitors(args: {
  // 0-10 competitor ASINs. Empty means single-listing keyword performance only (metrics.cps is then null). main_asin and duplicates are dropped silently and reported in skipped_asins.
  competitor_asins?: Array<string> | null;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // 1-300 keywords to evaluate. The cap matches find_keywords_with_multi_source's return cap, so its rows can be passed through whole. If you have more, pass the highest-priority_score subset.
  keyword_list: Array<{
  // The keyword phrase. Normalized for matching; echoed back verbatim.
  keyword: string;
  // Helium 10 keyword sources this phrase came from; it can come from several. Omit when it came from none — e.g. a phrase you added yourself.
  keyword_sources?: Array<"aba_converting_keywords" | "sqp_sales_keywords" | "top_keywords" | "opportunity_keywords" | "historical_top_keywords" | "top_performing_organic_keywords" | "top_performing_sponsored_keywords"> | null;
}>;
  // REQUIRED. Your product's ASIN (10-character alphanumeric). Always first in `asins` with is_mine=true.
  main_asin: string;
  // Unpublished copy to score for the main ASIN instead of its published listing. Field-by-field override.
  main_listing_draft?: {
  // Draft bullet points (at most 10).
  bullet_points?: Array<string> | null;
  // Draft description.
  description?: string | null;
  // Draft Item Highlight. Matched as part of the title, not as a separate location.
  item_highlight?: string | null;
  // Draft title.
  title?: string | null;
} | null;
  // REQUIRED. Marketplace short code (case-sensitive). No default — matching is language-specific. Confirm the marketplace with the user once, before the first Listing Builder call of the session; every later Listing Builder tool reuses that same value — do NOT ask the user for it again once it is set. Supported values: US, CA, MX, DE, ES, IT, FR, UK, IN, NL, AU, JP, BE, BR.
  marketplace: "US" | "CA" | "MX" | "DE" | "ES" | "IT" | "FR" | "UK" | "IN" | "NL" | "AU" | "JP" | "BE" | "BR";
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult>; };
```

## 51. `get_keywords_by_asin`

Helium 10 Keyword Research (formerly Cerebro). Reverse keyword search for one Amazon ASIN: returns every phrase where the ASIN is visible on Amazon — organic, sponsored, or in Amazon's recommendations — with search volume, rank, CPC, ABA top-3 click/conversion share, and H10 metrics (CPR, IQ score, title density). Use for keyword discovery from your own listing, competitor reverse-search, listing coverage audits, and PPC keyword selection. Not for: seed-keyword expansion with no ASIN (use get_keywords_by_keyword); scoring a finalized list (use analyze_keywords). Pass optional time_period (YYYY-MM) to look back at a single past month (e.g. last November's related keywords); omit for current data. One month per request; query each month separately for multi-month comparisons. `limit` accepts 1-10000 rows per call (default 150); up to 1000 rows per page are returned inline and a larger ask is delivered as a downloadable file instead (data.export.download_url) — set response_format='download' when the user explicitly wants a file. If data.pagination.has_more is true the result is incomplete: call again with `cursor` = data.pagination.next_cursor, but prefer narrowing the query over paginating through everything — this tool can page to at most 100,000 rows for one query, past which the result is truncated (truncation_reason='traversal_bound') and only a narrower query reaches the rest. Cursors expire 30 minutes after being issued and are bound to the query that issued them. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_keywords_by_asin(args: {
  // Single Amazon ASIN, 10-char alphanumeric (e.g. B01LR5S6HK).
  asin: string;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Opaque pagination cursor from a previous response's pagination.next_cursor. Omit for the first page. Pass it back verbatim with the same query arguments (limit may change between pages); never construct or edit it. Cursors are reusable but expire 30 minutes after being issued (pagination.cursor_expires_at) and are bound to the query that issued them.
  cursor?: string | null;
  // Optional. Default false: the search aggregates the ASIN's parent and all child variations. Pass true to cover only the exact ASIN given.
  exclude_variations?: boolean;
  // File format when results are delivered as a download (ignored for inline delivery). 'json' (default): gzip-compressed JSON, best for programmatic analysis. 'csv': UTF-8 CSV that opens directly in Excel/Sheets - prefer it when the user wants a spreadsheet/report file. Columns are the union of row fields; nested values stay JSON-encoded.
  export_format?: "json" | "csv";
  // Number of rows to return, 1-10000 (default 150); out-of-range values are rejected. Up to 1000 rows come back inline; ask for more and the window is delivered as a file instead (data.export.download_url), so size `limit` to what is actually wanted. A page can still come back shorter than `limit` when the rows are unusually large (the ~1MB inline budget); pagination.truncated says so and `cursor` continues from there. When pagination.has_more is true, fetch the next batch with `cursor`. Do NOT probe with a small limit to learn the dataset size — every page carries the same total_count/has_more metadata, so ask for the rows you actually need and read the metadata of that response.
  limit?: number;
  // Marketplace code: US, UK, DE, JP, etc. Must be in the Cerebro allowlist.
  marketplace: "US" | "CA" | "MX" | "DE" | "ES" | "IT" | "FR" | "UK" | "IN" | "NL" | "AU" | "JP" | "AE" | "BR" | "SA";
  // How results are delivered. 'auto' (default): rows are returned inline when they fit the response budget, otherwise they are exported to a file and data.export.download_url is returned instead — so simply request the amount of data you need via `limit`. 'inline': always inline, oversized pages are truncated and continue via cursor. 'download': always a downloadable file, even for small results; the file contains exactly the window selected by `limit`/`cursor`, so size `limit` to what is wanted (the tool's max for the whole dataset). Use 'download' when the user wants a file or when you need many rows for analysis (fetch the link in your execution environment instead of paging through the conversation).
  response_format?: "auto" | "inline" | "download";
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Optional historical month YYYY-MM (e.g. 2025-12). Omit for current data. One month per request; must fall within the 24-month lookback ending at the current UTC month.
  time_period?: string | null;
}): Promise<CallToolResult<{
  // Reverse ASIN search payload when the call succeeds. May be omitted or empty on failure—check for an ``error`` payload on failure.
  data: {
  // Present when results were delivered as a downloadable file (explicit response_format=download, or auto mode when the requested rows exceed the inline budget). download_url is for the user (hand it over) or for you (download in your execution environment to analyze the full data); never paste raw file contents into the conversation.
  export?: {
  // Column names appearing across the exported rows (after any projection); sparse fields may be absent on some rows.
  columns: Array<string>;
  // Time-limited HTTPS link to the gzip-compressed JSON export.
  download_url: string;
  // ISO-8601 UTC timestamp when download_url stops working.
  expires_at: string;
  // Exported file's format: 'json.gz' (gzip-compressed JSON, default) or 'csv' (UTF-8, opens directly in Excel), per the export_format argument.
  file_format: "json.gz" | "csv";
  // Number of rows contained in the exported file.
  row_count: number;
  // The first few exported rows (size-bounded; empty when even one row exceeds the sample budget). Use together with columns to describe the file contents to the user without downloading it.
  sample_rows: Array<{ [key: string]: unknown; }>;
} | null;
  // One row per search phrase the listing competes on for the ASIN you passed; includes organic, sponsored, and recommended ranks for that listing.
  keywords: Array<{
  // ABA Total Click Share — Keyword metrics, including click & conversion rates for the top 3 ASINs.
  aba_click_share?: number | null;
  // ABA Total Conv. Share — Gain valuable insights into the top 3 clicked products for each keyword;
  aba_conv_share?: number | null;
  // Amazon Choice — Badge / label context for the phrase (see Amazon Choice filter semantics in the Cerebro UI).
  amazon_choice?: string | null;
  // Amazon Rec. Rank — Keywords Amazon recommends for this ASIN, numbered from most to least recommended. Present only when the ASIN appears in that surface; absent = not ranked (not missing).
  amazon_recommended_rank?: number | null;
  // Competing Products — Total number of products returned in a customer search using this keyword/phrase.
  competing_products?: number;
  // CPR — Estimated number of units that need to be sold over 8 days, resulting from a search for this keyword in order for the product to rank on top half of page one for this keyword.
  cpr?: number;
  // The ASIN you ran reverse search for (query context for this row).
  current_asin?: string;
  // Cerebro IQ Score — Score based on ratio of estimated search volume vs. number of competing products: a high score means a relatively high number of searches compared to the number of competitors; a low score means the opposite.
  iq_score?: number;
  // Keyword Sales — Estimated number of weekly sales (in number of units) attributed to each keyword.
  keyword_sales_weekly?: number | null;
  // Match Type — Source from which a keyword is drawn (list of placement labels from search results).
  match_type?: Array<"organic" | "sponsored" | "ppc" | "section-amazons-choice" | "section-highly-rated" | "section-sponsored-brand-header" | "section-editorial-recommendations" | "section-sponsored-video" | "top-rated-from-our-brand" | "section-trending-now">;
  // Organic Rank — Organic search position detected for that keyword(s) for the analyzed product. Present only when the ASIN appears in that surface; absent = not ranked (not missing).
  organic_rank?: number | null;
  // Keyword Phrase — Keyword or phrase used by customers in search for a product.
  phrase?: string;
  // Internal phrase id linking this row to Helium 10 phrase metadata when present.
  phrase_id?: number;
  // Search Volume — Estimated number of times this keyword is searched monthly on Amazon.
  search_volume?: number;
  // Search Volume Trend — Over the past 30 days (percentage change as a number).
  search_volume_trend_30d_pct?: number | null;
  // Sponsored ASINs — Number of ASINs detected running sponsored ads recently in search results associated with this keyword phrase.
  sponsored_asins_count?: number;
  // Sponsored Rank — Sponsored ad position last detected in customer searches. Present only when the ASIN appears in that surface; absent = not ranked (not missing).
  sponsored_rank?: number | null;
  // Suggested PPC Bid — Suggested bid and bid range are calculated from a group of winning bids for ads that are similar to yours. You can choose to use the suggested bid, or any bid in or outside of the bid range.
  suggested_bid_usd?: number | null;
  // Title Density — Number of page 1 products with the searched keyword in their title.
  title_density?: number;
}>;
  // Pagination metadata for this page of rows. When has_more is true the result set is NOT complete — pass next_cursor back to fetch the next batch.
  pagination: {
  // Pagination consistency mode. 'live': every page queries live data (no snapshot); if the underlying data changes between pages, rows may shift across page boundaries - restart without `cursor` for a fresh view.
  consistency: string;
  // Rows delivered so far across this cursor chain, including this response (exported file rows count for download delivery).
  cumulative_count: number;
  // ISO-8601 UTC time when next_cursor stops being accepted; null when next_cursor is null. Expired cursors are rejected - restart from the first page by omitting `cursor`.
  cursor_expires_at: string | null;
  // True when more rows exist beyond this response.
  has_more: boolean;
  // Present only when the requested `limit` was adjusted: the value actually applied after clamping into [1, max].
  limit_applied?: number;
  // Present only when the requested `limit` was adjusted: the value as requested, before clamping.
  limit_requested?: number;
  // Opaque cursor for the next batch; pass it back verbatim as `cursor` with the same query arguments. Null when has_more is false. When truncation_reason is 'traversal_bound' this cursor is a terminal marker: passing it back returns a guidance error instead of rows - narrow the filters, and never restart from the first page to 'continue'.
  next_cursor: string | null;
  // Rows returned inline in this response (0 in download responses).
  returned_count: number;
  // Total rows matching the query, when known; null when the tail has not been reached yet (rely on has_more).
  total_count: number | null;
  // True when this response covers less than the requested window; see truncation_reason for why and how to continue.
  truncated: boolean;
  // Why truncated is true. 'size_limit': page cut to fit the inline response budget - continue with next_cursor. 'traversal_bound': this tool cannot paginate past its per-query row cap; next_cursor is a terminal marker (reusing it returns an error) - narrow the filters to reach the remaining rows, never restart from page 1. Null when truncated is false.
  truncation_reason: "size_limit" | "traversal_bound" | null;
};
  // One-sentence coverage note for this response (which rows are included and how to get the rest). Read it before concluding the data is complete.
  results_summary: string;
};
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
} | {
  // Structured error response from io-api for MCP endpoints.
  error: {
  // MCP string error code.
  code: string;
  // Optional structured error details.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Optional human-readable remediation guidance (may be omitted).
  remediation?: string;
};
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
}>>; };
```

## 52. `get_keywords_by_item_id`

Helium 10 Keyword Research for Walmart (Walmart Cerebro). Reverse keyword search for one Walmart item: returns every phrase the item ranks on at walmart.com, organic or sponsored, with the item's position on each phrase, how many products compete on it, how many run ads on it, and the phrase's Amazon search volume for cross-platform comparison. Use for: keyword discovery from a Walmart listing, competitor reverse-search on Walmart, Walmart listing coverage audits, and Walmart Sponsored Products keyword selection. Not for: Amazon products - use get_keywords_by_asin instead. Multi-item keyword comparison on Walmart - use compare_item_keywords. The two demand columns mean different things and are not comparable. search_trend_score is Walmart demand on a 0-100 relative scale (the "Search Trend Score" column in the Helium 10 UI) - NOT a number of searches, never report it as one. amazon_search_volume is the same phrase's estimated monthly searches on Amazon, an absolute number, provided for cross-platform comparison. Walmart also has no CPR, IQ Score, CPC, ABA click/conversion share, or title density - those exist on Amazon only. `limit` accepts 1-10000 rows per call (default 150); up to 1000 rows per page are returned inline and a larger ask is delivered as a downloadable file instead (data.export.download_url) — set response_format='download' when the user explicitly wants a file. If data.pagination.has_more is true the result is incomplete: call again with `cursor` = data.pagination.next_cursor, but prefer narrowing the query over paginating through everything — this tool can page to at most 100,000 rows for one query, past which the result is truncated (truncation_reason='traversal_bound') and only a narrower query reaches the rest. Cursors expire 30 minutes after being issued and are bound to the query that issued them. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_keywords_by_item_id(args: {
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Opaque pagination cursor from a previous response's pagination.next_cursor. Omit for the first page. Pass it back verbatim with the same query arguments (limit may change between pages); never construct or edit it. Cursors are reusable but expire 30 minutes after being issued (pagination.cursor_expires_at) and are bound to the query that issued them.
  cursor?: string | null;
  // Optional. Default false: the search aggregates the item and all its variants. Pass true to cover only the exact item ID given.
  exclude_variations?: boolean;
  // File format when results are delivered as a download (ignored for inline delivery). 'json' (default): gzip-compressed JSON, best for programmatic analysis. 'csv': UTF-8 CSV that opens directly in Excel/Sheets - prefer it when the user wants a spreadsheet/report file. Columns are the union of row fields; nested values stay JSON-encoded.
  export_format?: "json" | "csv";
  // Single Walmart item ID, all digits (e.g. 10450114). It is the numeric segment at the end of a walmart.com product URL. Amazon ASINs are not accepted here - use get_keywords_by_asin for Amazon.
  item_id: string;
  // Number of rows to return, 1-10000 (default 150); out-of-range values are rejected. Up to 1000 rows come back inline; ask for more and the window is delivered as a file instead (data.export.download_url), so size `limit` to what is actually wanted. A page can still come back shorter than `limit` when the rows are unusually large (the ~1MB inline budget); pagination.truncated says so and `cursor` continues from there. When pagination.has_more is true, fetch the next batch with `cursor`. Do NOT probe with a small limit to learn the dataset size — every page carries the same total_count/has_more metadata, so ask for the rows you actually need and read the metadata of that response.
  limit?: number;
  // How results are delivered. 'auto' (default): rows are returned inline when they fit the response budget, otherwise they are exported to a file and data.export.download_url is returned instead — so simply request the amount of data you need via `limit`. 'inline': always inline, oversized pages are truncated and continue via cursor. 'download': always a downloadable file, even for small results; the file contains exactly the window selected by `limit`/`cursor`, so size `limit` to what is wanted (the tool's max for the whole dataset). Use 'download' when the user wants a file or when you need many rows for analysis (fetch the link in your execution environment instead of paging through the conversation).
  response_format?: "auto" | "inline" | "download";
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{
  // Walmart reverse item-id search payload when the call succeeds. May be omitted or empty on failure—check for an ``error`` payload.
  data: {
  // Present when results were delivered as a downloadable file (explicit response_format=download, or auto mode when the requested rows exceed the inline budget). download_url is for the user (hand it over) or for you (download in your execution environment to analyze the full data); never paste raw file contents into the conversation.
  export?: {
  // Column names appearing across the exported rows (after any projection); sparse fields may be absent on some rows.
  columns: Array<string>;
  // Time-limited HTTPS link to the gzip-compressed JSON export.
  download_url: string;
  // ISO-8601 UTC timestamp when download_url stops working.
  expires_at: string;
  // Exported file's format: 'json.gz' (gzip-compressed JSON, default) or 'csv' (UTF-8, opens directly in Excel), per the export_format argument.
  file_format: "json.gz" | "csv";
  // Number of rows contained in the exported file.
  row_count: number;
  // The first few exported rows (size-bounded; empty when even one row exceeds the sample budget). Use together with columns to describe the file contents to the user without downloading it.
  sample_rows: Array<{ [key: string]: unknown; }>;
} | null;
  // One row per phrase the Walmart item ranks on at walmart.com, organic or sponsored. Sorted the way the Helium 10 Walmart Cerebro table sorts: organic rank ascending (unranked rows last), then Amazon search volume and Walmart search trend score descending. An item that ranks on nothing returns an empty array, not an error.
  keywords: Array<{
  // Search Volume — Estimated number of times this keyword is searched monthly on Amazon.
  amazon_search_volume?: number | null;
  // Competing Products — Total number of products returned in a customer search using this keyword/phrase on Walmart
  competing_products?: number | null;
  // Match Type — Source from which a keyword is drawn
  match_type?: Array<"organic" | "sponsored">;
  // Organic Rank — Organic position last detected for analyzed product in customer search on Walmart
  organic_rank?: number | null;
  // Keyword Phrase — Keyword for which the product appears in top 500 positions in Walmart search results
  phrase?: string;
  // Internal phrase id linking this row to Helium 10 phrase metadata when present.
  phrase_id?: number;
  // Search Trend Score — This score represents the relative popularity of search terms on Walmart, normalized on a scale from 0 to 100.
  search_trend_score?: number;
  // Sponsored Products — Number of products detected running sponsored ads in search results for this keyword phrase on Walmart
  sponsored_products_count?: number | null;
  // Sponsored Rank — Sponsored ad position last detected for analyzed product in customer search on Walmart
  sponsored_rank?: number | null;
}>;
  // Pagination metadata for this page of rows. When has_more is true the result set is NOT complete — pass next_cursor back to fetch the next batch.
  pagination: {
  // Pagination consistency mode. 'live': every page queries live data (no snapshot); if the underlying data changes between pages, rows may shift across page boundaries - restart without `cursor` for a fresh view.
  consistency: string;
  // Rows delivered so far across this cursor chain, including this response (exported file rows count for download delivery).
  cumulative_count: number;
  // ISO-8601 UTC time when next_cursor stops being accepted; null when next_cursor is null. Expired cursors are rejected - restart from the first page by omitting `cursor`.
  cursor_expires_at: string | null;
  // True when more rows exist beyond this response.
  has_more: boolean;
  // Present only when the requested `limit` was adjusted: the value actually applied after clamping into [1, max].
  limit_applied?: number;
  // Present only when the requested `limit` was adjusted: the value as requested, before clamping.
  limit_requested?: number;
  // Opaque cursor for the next batch; pass it back verbatim as `cursor` with the same query arguments. Null when has_more is false. When truncation_reason is 'traversal_bound' this cursor is a terminal marker: passing it back returns a guidance error instead of rows - narrow the filters, and never restart from the first page to 'continue'.
  next_cursor: string | null;
  // Rows returned inline in this response (0 in download responses).
  returned_count: number;
  // Total rows matching the query, when known; null when the tail has not been reached yet (rely on has_more).
  total_count: number | null;
  // True when this response covers less than the requested window; see truncation_reason for why and how to continue.
  truncated: boolean;
  // Why truncated is true. 'size_limit': page cut to fit the inline response budget - continue with next_cursor. 'traversal_bound': this tool cannot paginate past its per-query row cap; next_cursor is a terminal marker (reusing it returns an error) - narrow the filters to reach the remaining rows, never restart from page 1. Null when truncated is false.
  truncation_reason: "size_limit" | "traversal_bound" | null;
};
  // One-sentence coverage note for this response (which rows are included and how to get the rest). Read it before concluding the data is complete.
  results_summary: string;
};
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
} | {
  // Structured error response from io-api for MCP endpoints.
  error: {
  // MCP string error code.
  code: string;
  // Optional structured error details.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Optional human-readable remediation guidance (may be omitted).
  remediation?: string;
};
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
}>>; };
```

## 53. `get_keywords_by_keyword`

Helium 10 Keyword Research (formerly Magnet). Seed-keyword expansion for one keyword: returns related long-tail and derivative phrases with search volume, relationship type, CPC, ABA top-3 click/conversion share, and H10 metrics (CPR, IQ score, title density). Use for keyword planning from a single concept, PPC keyword selection, listing keyword coverage, and hero-keyword discovery. Not for: reverse search on an ASIN (use get_keywords_by_asin); scoring a finalized list (use analyze_keywords). Pass optional time_period (YYYY-MM) to look back at a single past month (e.g. last November's related keywords); omit for current data. One month per request; query each month separately for multi-month comparisons. `limit` accepts 1-10000 rows per call (default 150); up to 1000 rows per page are returned inline and a larger ask is delivered as a downloadable file instead (data.export.download_url) — set response_format='download' when the user explicitly wants a file. If data.pagination.has_more is true the result is incomplete: call again with `cursor` = data.pagination.next_cursor, but prefer narrowing the query over paginating through everything — this tool can page to at most 100,000 rows for one query, past which the result is truncated (truncation_reason='traversal_bound') and only a narrower query reaches the rest. Cursors expire 30 minutes after being issued and are bound to the query that issued them. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_keywords_by_keyword(args: {
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Opaque pagination cursor from a previous response's pagination.next_cursor. Omit for the first page. Pass it back verbatim with the same query arguments (limit may change between pages); never construct or edit it. Cursors are reusable but expire 30 minutes after being issued (pagination.cursor_expires_at) and are bound to the query that issued them.
  cursor?: string | null;
  // File format when results are delivered as a download (ignored for inline delivery). 'json' (default): gzip-compressed JSON, best for programmatic analysis. 'csv': UTF-8 CSV that opens directly in Excel/Sheets - prefer it when the user wants a spreadsheet/report file. Columns are the union of row fields; nested values stay JSON-encoded.
  export_format?: "json" | "csv";
  // Number of rows to return, 1-10000 (default 150); out-of-range values are rejected. Up to 1000 rows come back inline; ask for more and the window is delivered as a file instead (data.export.download_url), so size `limit` to what is actually wanted. A page can still come back shorter than `limit` when the rows are unusually large (the ~1MB inline budget); pagination.truncated says so and `cursor` continues from there. When pagination.has_more is true, fetch the next batch with `cursor`. Do NOT probe with a small limit to learn the dataset size — every page carries the same total_count/has_more metadata, so ask for the rows you actually need and read the metadata of that response.
  limit?: number;
  // Marketplace code: US, UK, DE, JP, etc. Must be in the Magnet allowlist.
  marketplace: "US" | "CA" | "MX" | "DE" | "ES" | "IT" | "FR" | "UK" | "IN" | "NL" | "AU" | "JP" | "AE" | "BR" | "SA";
  // How results are delivered. 'auto' (default): rows are returned inline when they fit the response budget, otherwise they are exported to a file and data.export.download_url is returned instead — so simply request the amount of data you need via `limit`. 'inline': always inline, oversized pages are truncated and continue via cursor. 'download': always a downloadable file, even for small results; the file contains exactly the window selected by `limit`/`cursor`, so size `limit` to what is wanted (the tool's max for the whole dataset). Use 'download' when the user wants a file or when you need many rows for analysis (fetch the link in your execution environment instead of paging through the conversation).
  response_format?: "auto" | "inline" | "download";
  // Single seed keyword, e.g. 'apple watch bands for women'.
  seed_keyword: string;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Optional historical month YYYY-MM (e.g. 2025-11). Omit for current data. One month per request; must fall within the 24-month lookback ending at the current UTC month.
  time_period?: string | null;
}): Promise<CallToolResult<{
  // Single-seed expansion payload when the call succeeds. May be omitted or empty on failure—check for an ``error`` payload.
  data: {
  // Present when results were delivered as a downloadable file (explicit response_format=download, or auto mode when the requested rows exceed the inline budget). download_url is for the user (hand it over) or for you (download in your execution environment to analyze the full data); never paste raw file contents into the conversation.
  export?: {
  // Column names appearing across the exported rows (after any projection); sparse fields may be absent on some rows.
  columns: Array<string>;
  // Time-limited HTTPS link to the gzip-compressed JSON export.
  download_url: string;
  // ISO-8601 UTC timestamp when download_url stops working.
  expires_at: string;
  // Exported file's format: 'json.gz' (gzip-compressed JSON, default) or 'csv' (UTF-8, opens directly in Excel), per the export_format argument.
  file_format: "json.gz" | "csv";
  // Number of rows contained in the exported file.
  row_count: number;
  // The first few exported rows (size-bounded; empty when even one row exceeds the sample budget). Use together with columns to describe the file contents to the user without downloading it.
  sample_rows: Array<{ [key: string]: unknown; }>;
} | null;
  // Related phrases and metrics discovered starting from one seed term in the chosen marketplace (Magnet expansion flow).
  keywords: Array<{
  // ABA Total Click Share — Keyword metrics, including click & conversion rates for the top 3 ASINs.
  aba_click_share?: number | null;
  // ABA Total Conv. Share — Gain valuable insights into the top 3 clicked products for each keyword;
  aba_conv_share?: number | null;
  // Amazon Choice — Badge / label context for the phrase (see Amazon Choice filter semantics in the Cerebro UI).
  amazon_choice?: string | null;
  // Competing Products — Total number of products returned in a customer search using this keyword/phrase.
  competing_products?: number;
  // CPR — Estimated number of units that need to be sold over 8 days, resulting from a search for this keyword in order for the product to rank on top half of page one for this keyword. Integer when whole, float otherwise — parse as numeric, not strictly float.
  cpr?: number | number;
  // Cerebro IQ Score — Score based on ratio of estimated search volume vs. number of competing products: a high score means a relatively high number of searches compared to the number of competitors; a low score means the opposite.
  iq_score?: number;
  // Keyword Sales — Estimated number of weekly sales (in number of units) attributed to each keyword
  keyword_sales_weekly?: number | null;
  // Match Type — Source from which a keyword is drawn (list of placement labels from search results).
  match_type?: Array<"contextual" | "lexical" | "sponsored" | "ppc" | "section-amazons-choice" | "section-highly-rated" | "section-sponsored-brand-header" | "section-editorial-recommendations" | "section-sponsored-video" | "top-rated-from-our-brand" | "section-trending-now" | "organic">;
  // Keyword Phrase — Keyword or phrase used by customers in search for a product.
  phrase?: string;
  // Internal phrase id linking this row to Helium 10 phrase metadata when present.
  phrase_id?: number;
  // Search Volume — Estimated number of times this keyword is searched monthly on Amazon.
  search_volume?: number;
  // Search Volume Trend — Over the past 30 days (percentage change as a number).
  search_volume_trend_30d_pct?: number | null;
  // Sponsored ASINs — Number of ASINs detected running sponsored ads recently in search results associated with this keyword phrase.
  sponsored_asins_count?: number;
  // Suggested PPC Bid — Suggested bid and bid range are calculated from a group of winning bids for ads that are similar to yours. You can choose to use the suggested bid, or any bid in or outside of the bid range.
  suggested_bid_usd?: number | null;
  // Title Density — Number of page 1 products with the searched keyword in their title.
  title_density?: number;
}>;
  // Pagination metadata for this page of rows. When has_more is true the result set is NOT complete — pass next_cursor back to fetch the next batch.
  pagination: {
  // Pagination consistency mode. 'live': every page queries live data (no snapshot); if the underlying data changes between pages, rows may shift across page boundaries - restart without `cursor` for a fresh view.
  consistency: string;
  // Rows delivered so far across this cursor chain, including this response (exported file rows count for download delivery).
  cumulative_count: number;
  // ISO-8601 UTC time when next_cursor stops being accepted; null when next_cursor is null. Expired cursors are rejected - restart from the first page by omitting `cursor`.
  cursor_expires_at: string | null;
  // True when more rows exist beyond this response.
  has_more: boolean;
  // Present only when the requested `limit` was adjusted: the value actually applied after clamping into [1, max].
  limit_applied?: number;
  // Present only when the requested `limit` was adjusted: the value as requested, before clamping.
  limit_requested?: number;
  // Opaque cursor for the next batch; pass it back verbatim as `cursor` with the same query arguments. Null when has_more is false. When truncation_reason is 'traversal_bound' this cursor is a terminal marker: passing it back returns a guidance error instead of rows - narrow the filters, and never restart from the first page to 'continue'.
  next_cursor: string | null;
  // Rows returned inline in this response (0 in download responses).
  returned_count: number;
  // Total rows matching the query, when known; null when the tail has not been reached yet (rely on has_more).
  total_count: number | null;
  // True when this response covers less than the requested window; see truncation_reason for why and how to continue.
  truncated: boolean;
  // Why truncated is true. 'size_limit': page cut to fit the inline response budget - continue with next_cursor. 'traversal_bound': this tool cannot paginate past its per-query row cap; next_cursor is a terminal marker (reusing it returns an error) - narrow the filters to reach the remaining rows, never restart from page 1. Null when truncated is false.
  truncation_reason: "size_limit" | "traversal_bound" | null;
};
  // One-sentence coverage note for this response (which rows are included and how to get the rest). Read it before concluding the data is complete.
  results_summary: string;
};
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
} | {
  // Structured error response from io-api for MCP endpoints.
  error: {
  // MCP string error code.
  code: string;
  // Optional structured error details.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Optional human-readable remediation guidance (may be omitted).
  remediation?: string;
};
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
}>>; };
```

## 54. `get_keywords_cpc_history`

Helium 10 keyword CPC / suggested PPC bid history (not a Black Box Web module). Returns weekly suggested-PPC-bid points for one keyword (keyword): cpc, low_cpc, high_cpc as of each event date, plus currency_code (the marketplace's local currency; values are not a single fixed currency across marketplaces). cpc, low_cpc, and high_cpc are integers in the minor unit of currency_code (e.g. 79 = USD 0.79); Japan (JPY) has no minor unit, so its values are whole yen. Default last 30 days when both dates omitted. Pass start_date and end_date together (both YYYY-MM-DD within the last 3 years), or omit both for the default last 30 days; do not pass only one date. Use when the user asks for CPC or PPC bid history/trend for a keyword. Not for search volume history (use get_keywords_search_volume_history), ASIN price/BSR/reviews history, keyword ranking history, current/snapshot CPC already present in search results, or batch multi-keyword calls. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_keywords_cpc_history(args: {
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Optional window end (inclusive), format yyyy-MM-dd (UTC day), no later than today. Pass it together with start_date, both within the last 3 years, or omit both dates for the default last 30 days. Do not pass only one date.
  end_date?: string | null;
  // Required. Keyword to fetch CPC history for: phrase text (e.g. 'wireless earbuds') or a numeric Helium 10 phrase id (digit-only input is treated as a phrase id). 1-500 characters.
  keyword: string;
  // Required. Amazon marketplace code. Allowed values: US, CA, MX, DE, ES, IT, FR, UK, IN, NL, AU, JP, AE, BR, SA.
  marketplace: "US" | "CA" | "MX" | "DE" | "ES" | "IT" | "FR" | "UK" | "IN" | "NL" | "AU" | "JP" | "AE" | "BR" | "SA";
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Optional window start (inclusive), format yyyy-MM-dd (UTC day). Pass it together with end_date, both within the last 3 years, or omit both dates for the default last 30 days. Do not pass only one date.
  start_date?: string | null;
}): Promise<CallToolResult<{
  // Success payload; full schema in the top-level data property.
  data: { [key: string]: unknown; };
  // No error on success.
  error: null;
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
} | {
  // No data when the tool fails.
  data: null;
  // Error payload; full schema in the top-level error property.
  error: { [key: string]: unknown; };
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
}>>; };
```

## 55. `get_keywords_new_suggestion`

Helium 10 New Keywords to Track. For a given ASIN, returns keywords NOT yet in your Keyword Tracker — drawn from the ASIN's own footprint and its tracked competitors' footprints — with search volume, keyword_sales, CPS, ABA shares, and a source field (main product vs a specific competitor). Use to expand your tracked set after a listing change, find high-potential terms you're missing, and for tracking hygiene. Candidates only — add them in the Helium 10 UI. Not for: already-tracked metrics (use list_tracked_keywords); reverse search with no tracking context (use get_keywords_by_asin); competitor comparison (use list_tracked_competitor_keywords). Errors: invalid_input / invalid_asin / no_result / no_data / rate_limited / upstream_timeout. Returns up to `limit` rows per call (default 100, max 40000); one page inlines at most ~500 rows / ~1MB, and oversized pages are truncated and continue via cursor. If data.pagination.has_more is true the result is incomplete: call again with `cursor` = data.pagination.next_cursor, but prefer narrowing filters over paginating through everything. Cursors expire 30 minutes after being issued and are bound to the query that issued them. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_keywords_new_suggestion(args: { asin_or_url: string; context?: string; cursor?: string | null; limit?: number; marketplace?: "US" | "CA" | "MX" | "BR" | "DE" | "ES" | "FR" | "IT" | "UK" | "NL" | "TR" | "IN" | "AE" | "EG" | "SA" | "SE" | "PL" | "JP" | "AU" | "SG" | "CN" | "BE" | "ZA" | "IE" | null; session_id?: string; sort_direction?: "asc" | "desc" | null; sort_field?: "keywords_phrase" | "search_volume" | "keyword_sales" | "organic_rank" | "sponsored_rank" | "relative_rank" | "competitor_performance_score" | "aba_total_clicks_share" | "aba_total_conver_share" | null; }): Promise<CallToolResult<{
  // Keyword Tracker tool payload when the call completes; interpret together with ``code``.
  data?: {
  // Marketplace short code (e.g. `US`, `UK`, `DE`) for the Amazon store this result applies to.
  marketplace?: string;
  // Pagination metadata for this page of rows. When has_more is true the result set is NOT complete — pass next_cursor back to fetch the next batch.
  pagination: {
  // Pagination consistency mode. 'live': every page queries live data (no snapshot); if the underlying data changes between pages, rows may shift across page boundaries - restart without `cursor` for a fresh view.
  consistency: string;
  // Rows delivered so far across this cursor chain, including this response (exported file rows count for download delivery).
  cumulative_count: number;
  // ISO-8601 UTC time when next_cursor stops being accepted; null when next_cursor is null. Expired cursors are rejected - restart from the first page by omitting `cursor`.
  cursor_expires_at: string | null;
  // True when more rows exist beyond this response.
  has_more: boolean;
  // Present only when the requested `limit` was adjusted: the value actually applied after clamping into [1, max].
  limit_applied?: number;
  // Present only when the requested `limit` was adjusted: the value as requested, before clamping.
  limit_requested?: number;
  // Opaque cursor for the next batch; pass it back verbatim as `cursor` with the same query arguments. Null when has_more is false. When truncation_reason is 'traversal_bound' this cursor is a terminal marker: passing it back returns a guidance error instead of rows - narrow the filters, and never restart from the first page to 'continue'.
  next_cursor: string | null;
  // Rows returned inline in this response (0 in download responses).
  returned_count: number;
  // Total rows matching the query, when known; null when the tail has not been reached yet (rely on has_more).
  total_count: number | null;
  // True when this response covers less than the requested window; see truncation_reason for why and how to continue.
  truncated: boolean;
  // Why truncated is true. 'size_limit': page cut to fit the inline response budget - continue with next_cursor. 'traversal_bound': this tool cannot paginate past its per-query row cap; next_cursor is a terminal marker (reusing it returns an error) - narrow the filters to reach the remaining rows, never restart from page 1. Null when truncated is false.
  truncation_reason: "size_limit" | "traversal_bound" | null;
};
  // One-sentence coverage note for this response (which rows are included and how to get the rest). Read it before concluding the data is complete.
  results_summary: string;
  // Keyword-level rows for this listing or account slice.
  rows: Array<{
  // Keyword metrics, including click & conversion rates for the top 3 ASINs; may be empty when the feature or plan does not expose it. Gating: Diamond+ plan AND Brand Registry required; null otherwise.
  aba_total_clicks_share?: string | null;
  // Gain valuable insights into the top 3 clicked products for each keyword; may be empty when the feature or plan does not expose it. Gating: Diamond+ plan AND Brand Registry required; null otherwise.
  aba_total_conver_share?: string | null;
  // Product this suggestion list is scoped to.
  asin?: string | null;
  // Competitor Performance Index is a measure how successful the listed competitors are as a group for the given keyword.
  cps?: string | null;
  // Estimated number of weekly sales (in number of units) attributed to each keyword. Gating: Platinum+ plan AND North America marketplace only; field absent otherwise.
  keyword_sales?: number | number | null;
  // Keyword or phrase used by customers in search for a product.
  keywords_phrase?: string | null;
  // Estimated number of times this keyword is searched monthly on Amazon.
  search_volume?: number | number | null;
  // Summarizes how this keyword qualified as a suggestion.
  source?: {
  // Total competitors counted.
  competitor_asins_count?: number | number | null;
  // Which tracked competitors also rank in that window.
  competitor_asins_in_positions_range?: Array<string> | null;
  // Whether YOUR product ranks within the evaluation window.
  my_product_in_positions_range?: boolean | null;
} | null;
}>;
  // Which Keyword Tracker capability produced this payload: tracked keywords, competitor comparison, or new suggestions.
  tool?: string;
} | null;
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
}>>; };
```

## 56. `get_keywords_rank_history`

Helium 10 Keyword Tracker. Returns historical sponsored and organic rank trends for one keyword (fuzzy match) over a date range, scoped to an ASIN/product URL — summary metrics (top / lowest / average for both surfaces) plus full time-series rank history. Use to analyze rank movement over time, find peak ad-placement periods, detect organic-rank decline, and evaluate phased ranking performance. Not for: current snapshots across all tracked keywords (use list_tracked_keywords); discovering new keywords (use get_keywords_by_asin); batch metrics across a list (use analyze_keywords). Errors: invalid_input / invalid_asin / no_result / no_data / rate_limited / upstream_timeout. Returns up to `limit` rows per call (default 100, max 500); one page inlines at most ~500 rows / ~1MB, and oversized pages are truncated and continue via cursor. If data.pagination.has_more is true the result is incomplete: call again with `cursor` = data.pagination.next_cursor, but prefer narrowing filters over paginating through everything. Cursors expire 30 minutes after being issued and are bound to the query that issued them. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_keywords_rank_history(args: { asin_or_url?: string | null; context?: string; cursor?: string | null; end_date?: string | null; keyword: string; limit?: number; marketplace?: "US" | "CA" | "MX" | "BR" | "DE" | "ES" | "FR" | "IT" | "UK" | "NL" | "TR" | "IN" | "AE" | "EG" | "SA" | "SE" | "PL" | "JP" | "AU" | "SG" | "CN" | "BE" | "ZA" | "IE" | null; session_id?: string; start_date?: string | null; }): Promise<CallToolResult<{
  // Keyword Tracker tool payload when the call completes; interpret together with ``code``.
  data?: {
  // Marketplace short code (e.g. `US`, `UK`, `DE`) for the Amazon store this result applies to.
  marketplace?: string;
  // Pagination metadata for this page of rows. When has_more is true the result set is NOT complete — pass next_cursor back to fetch the next batch.
  pagination: {
  // Pagination consistency mode. 'live': every page queries live data (no snapshot); if the underlying data changes between pages, rows may shift across page boundaries - restart without `cursor` for a fresh view.
  consistency: string;
  // Rows delivered so far across this cursor chain, including this response (exported file rows count for download delivery).
  cumulative_count: number;
  // ISO-8601 UTC time when next_cursor stops being accepted; null when next_cursor is null. Expired cursors are rejected - restart from the first page by omitting `cursor`.
  cursor_expires_at: string | null;
  // True when more rows exist beyond this response.
  has_more: boolean;
  // Present only when the requested `limit` was adjusted: the value actually applied after clamping into [1, max].
  limit_applied?: number;
  // Present only when the requested `limit` was adjusted: the value as requested, before clamping.
  limit_requested?: number;
  // Opaque cursor for the next batch; pass it back verbatim as `cursor` with the same query arguments. Null when has_more is false. When truncation_reason is 'traversal_bound' this cursor is a terminal marker: passing it back returns a guidance error instead of rows - narrow the filters, and never restart from the first page to 'continue'.
  next_cursor: string | null;
  // Rows returned inline in this response (0 in download responses).
  returned_count: number;
  // Total rows matching the query, when known; null when the tail has not been reached yet (rely on has_more).
  total_count: number | null;
  // True when this response covers less than the requested window; see truncation_reason for why and how to continue.
  truncated: boolean;
  // Why truncated is true. 'size_limit': page cut to fit the inline response budget - continue with next_cursor. 'traversal_bound': this tool cannot paginate past its per-query row cap; next_cursor is a terminal marker (reusing it returns an error) - narrow the filters to reach the remaining rows, never restart from page 1. Null when truncated is false.
  truncation_reason: "size_limit" | "traversal_bound" | null;
};
  // One-sentence coverage note for this response (which rows are included and how to get the rest). Read it before concluding the data is complete.
  results_summary: string;
  // Keyword-level rows for this listing or account slice.
  rows: Array<{
  // Product ASIN this rank-history row is scoped to.
  asin?: string | null;
  // Tracked keyword phrase that this row reports rank history for.
  keywords_phrase?: string | null;
  // Average organic position over the window.
  organic_rank_avg?: number | number | null;
  // Organic rank time series over the window, ordered by `date_time`. A single calendar day may contain multiple points when more than one snapshot was recorded.
  organic_rank_history?: Array<{
  // Measurement instant in UTC ISO-8601 format, e.g. `2026-05-25T08:47:18Z`.
  date_time?: string | null;
  // Rank position at that instant; null when the listing did not surface. null = not ranked (not "data missing").
  rank?: number | number | null;
}> | null;
  // Worst (numerically highest) organic position observed in the window.
  organic_rank_lowest?: number | number | null;
  // Best (numerically lowest) organic position observed in the window.
  organic_rank_top?: number | number | null;
  // Average sponsored ad position over the window.
  sponsored_rank_avg?: number | number | null;
  // Sponsored rank time series over the window, ordered by `date_time`. A single calendar day may contain multiple points when more than one snapshot was recorded.
  sponsored_rank_history?: Array<{
  // Measurement instant in UTC ISO-8601 format, e.g. `2026-05-25T08:47:18Z`.
  date_time?: string | null;
  // Rank position at that instant; null when the listing did not surface. null = not ranked (not "data missing").
  rank?: number | number | null;
}> | null;
  // Worst (numerically highest) sponsored ad position observed in the window.
  sponsored_rank_lowest?: number | number | null;
  // Best (numerically lowest) sponsored ad position observed in the window.
  sponsored_rank_top?: number | number | null;
}>;
  // Which Keyword Tracker capability produced this payload: tracked keywords, competitor comparison, or new suggestions.
  tool?: string;
} | null;
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
}>>; };
```

## 57. `get_keywords_sales_history`

Keywords sales history. Returns weekly sales-units points for one keyword (keyword); each point is the sales units attributed to that keyword for the week starting on that event date. Each point has period_start/period_end (period_start plus 6 days) alongside sales. Default last 30 days when both dates omitted. Pass start_date and end_date together (both YYYY-MM-DD within the last 3 years), or omit both for the default last 30 days; do not pass only one date. Use when the user asks for sales history or sales trend for a keyword. Not for ASIN price/BSR/reviews, keyword search volume, keyword ranking, or batch multi-keyword. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_keywords_sales_history(args: {
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Optional window end (inclusive), format yyyy-MM-dd (UTC day), no later than today. Pass it together with start_date, both within the last 3 years, or omit both dates for the default last 30 days. Do not pass only one date.
  end_date?: string | null;
  // Required. Keyword to fetch sales history for: phrase text (e.g. 'wireless earbuds') or a numeric Helium 10 phrase id (digit-only input is treated as a phrase id).
  keyword: string;
  // Required. Amazon marketplace code. Allowed values: US, CA, MX, DE, ES, IT, FR, UK, IN, NL, AU, JP, AE, BR, SA.
  marketplace: "US" | "CA" | "MX" | "DE" | "ES" | "IT" | "FR" | "UK" | "IN" | "NL" | "AU" | "JP" | "AE" | "BR" | "SA";
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Optional window start (inclusive), format yyyy-MM-dd (UTC day). Pass it together with end_date, both within the last 3 years, or omit both dates for the default last 30 days. Do not pass only one date.
  start_date?: string | null;
}): Promise<CallToolResult<{
  // Success payload; full schema in the top-level data property.
  data: { [key: string]: unknown; };
  // No error on success.
  error: null;
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
} | {
  // No data when the tool fails.
  data: null;
  // Error payload; full schema in the top-level error property.
  error: { [key: string]: unknown; };
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
}>>; };
```

## 58. `get_keywords_search_frequency_rank_history`

Keywords search frequency rank history (Amazon Brand Analytics). Returns search-frequency-rank points for one keyword (keyword), one point per reporting period; lower rank means the keyword is searched more frequently. Each point has period_start/period_end (that week/month's calendar boundaries) plus search_frequency_rank. reporting_range (weekly or monthly) is required. Default last 30 days when both dates omitted. Pass start_date and end_date together (both YYYY-MM-DD within the last 3 years), or omit both for the default last 30 days; do not pass only one date. Use when the user asks for search frequency rank or Brand Analytics rank trend for a keyword. Not for ASIN price/BSR/reviews, keyword search volume, keyword sales, or batch multi-keyword. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_keywords_search_frequency_rank_history(args: {
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Optional window end (inclusive), format yyyy-MM-dd (UTC day), no later than today. Pass it together with start_date, both within the last 3 years, or omit both dates for the default last 30 days. Do not pass only one date.
  end_date?: string | null;
  // Required. Keyword to fetch search frequency rank history for: phrase text (e.g. 'wireless earbuds') or a numeric Helium 10 phrase id (digit-only input is treated as a phrase id).
  keyword: string;
  // Required. Amazon marketplace code. Allowed values: US, CA, MX, DE, ES, IT, FR, UK, IN, NL, AU, JP, AE, BR, SA.
  marketplace: "US" | "CA" | "MX" | "DE" | "ES" | "IT" | "FR" | "UK" | "IN" | "NL" | "AU" | "JP" | "AE" | "BR" | "SA";
  // Required. Reporting period granularity for the rank series: weekly or monthly.
  reporting_range: "weekly" | "monthly";
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Optional window start (inclusive), format yyyy-MM-dd (UTC day). Pass it together with end_date, both within the last 3 years, or omit both dates for the default last 30 days. Do not pass only one date.
  start_date?: string | null;
}): Promise<CallToolResult<{
  // Success payload; full schema in the top-level data property.
  data: { [key: string]: unknown; };
  // No error on success.
  error: null;
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
} | {
  // No data when the tool fails.
  data: null;
  // Error payload; full schema in the top-level error property.
  error: { [key: string]: unknown; };
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
}>>; };
```

## 59. `get_keywords_search_volume_history`

Keywords search volume history (not a Black Box Web module). Returns weekly search-volume points for one keyword (keyword); each point is the trailing 30-day search volume for that week, and data updates about weekly. Default last 30 days when both dates omitted. Pass start_date and end_date together (both YYYY-MM-DD within the last 3 years), or omit both for the default last 30 days; do not pass only one date. Use when the user asks for search volume history or demand trend. Not for ASIN price/BSR/reviews, keyword ranking, batch multi-keyword, or keyword discovery rows (use search_amazon_keywords). This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_keywords_search_volume_history(args: {
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Optional window end (inclusive), format yyyy-MM-dd (UTC day), no later than today. Pass it together with start_date, both within the last 3 years, or omit both dates for the default last 30 days. Do not pass only one date.
  end_date?: string | null;
  // Required. Keyword to fetch search volume history for: phrase text (e.g. 'wireless earbuds') or a numeric Helium 10 phrase id (digit-only input is treated as a phrase id).
  keyword: string;
  // Required. Amazon marketplace code. Allowed values: US, CA, MX, DE, ES, IT, FR, UK, IN, NL, AU, JP, AE, BR, SA.
  marketplace: "US" | "CA" | "MX" | "DE" | "ES" | "IT" | "FR" | "UK" | "IN" | "NL" | "AU" | "JP" | "AE" | "BR" | "SA";
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Optional window start (inclusive), format yyyy-MM-dd (UTC day). Pass it together with end_date, both within the last 3 years, or omit both dates for the default last 30 days. Do not pass only one date.
  start_date?: string | null;
}): Promise<CallToolResult<{
  // Success payload; full schema in the top-level data property.
  data: { [key: string]: unknown; };
  // No error on success.
  error: null;
} | {
  // No data when the tool fails.
  data: null;
  // Error payload; full schema in the top-level error property.
  error: { [key: string]: unknown; };
}>>; };
```

## 60. `get_listing_details`

Helium 10 Listing Analyzer. For one Amazon ASIN, returns the complete listing profile: title, Listing Quality Score (LQS), age, image count, variation count, category & subcategory BSR, seller name & region, price, coupon status, parent- and ASIN-level sales with revenue estimates, review count, sales-to-review, shipping details, weight, storage fee, top-10 keywords with search volumes, and variation relationships with parent/child info (a child ASIN input also works). Use as the primary single-ASIN lookup for listing audits, pre-launch benchmarking, variation-family discovery, and competitive research on a specific product. Not for: comparing multiple ASINs (use compare_listings); the LQS pass/fail checklist (use get_listing_score); keyword strategy (use get_top_keywords); writing / generating / optimizing / rewriting listing copy — title, bullets, or description (that is the Listing Builder flow, not this tool). This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_listing_details(args: {
  // Alias for ``main_asin`` (accepted because callers often send ``asin``). Prefer ``main_asin``; when both are supplied ``main_asin`` wins. Leave unset if you already pass ``main_asin``.
  asin?: string | null;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Main Amazon ASIN to analyze (10 characters). This is the seed listing whose full profile is returned along with the three chart bundles. Required — the parameter is named ``main_asin`` (not ``asin``); you may also pass it via the ``asin`` alias.
  main_asin?: string | null;
  // Optional marketplace short code where the ASIN is listed. Supported: US, CA, MX, DE, ES, IT, FR, UK, IN, NL, AE, BR, AU. Defaults to US when omitted. Case-insensitive; whitespace is trimmed.
  marketplace?: string;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult>; };
```

## 61. `get_listing_score`

Helium 10 Listing Quality Audit. For an ASIN group (main + up to 10 competitors), returns the session-average LQS (0–10) with aggregate pass/fail counts per criterion, plus individual LQS scores and pass/fail results for all 13 criteria per ASIN — across Copy, Media, and Review (title symbols/length, ≥5 bullets, bullet length, image resolution & count, white-background main image, video presence, description ≥1000 chars or A+, ≥20 reviews, ≥4-star rating). All ASINs returned in one call; the agent surfaces whichever ASIN the user asks about. Use to understand content-compliance scores, pinpoint which elements drag LQS down, and compare listing quality across competitors. Not for: full attribute lookup (use get_listing_details); a quick LQS number alongside other metrics (use compare_listings). This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_listing_score(args: {
  // Alias for ``main_asin`` (accepted because callers often send ``asin``). Prefer ``main_asin``; when both are supplied ``main_asin`` wins. Leave unset if you already pass ``main_asin``.
  asin?: string | null;
  // Optional list of up to 10 competitor ASINs (each 10 characters). When provided, ``score_data`` gains one block per competitor and ``summary_data`` reports the cross-ASIN ``average_lqs`` plus per-metric ``asins_passed`` / ``asins_failed`` lists.
  competitor_asins?: Array<string> | null;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Main Amazon ASIN to score (10 characters). Its own score block (``lqs`` + per-metric pass/fail) is always returned under ``score_data.<asin>``. Required — the parameter is named ``main_asin`` (not ``asin``); you may also pass it via the ``asin`` alias.
  main_asin?: string | null;
  // Optional marketplace short code where the ASIN is listed. Supported: US, CA, MX, DE, ES, IT, FR, UK, IN, NL, AE, BR, AU. Defaults to US when omitted. Case-insensitive; whitespace is trimmed.
  marketplace?: string;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{
  // Listing Quality Score (LQS) analysis per ASIN: ``score_data`` keys each ASIN to its ``lqs`` plus a per-metric pass / fail breakdown (single mode carries just the main ASIN; multi mode carries every session ASIN). ``summary_data`` (multi mode only) adds the cross-ASIN ``average_lqs`` and per-metric passed / failed ASIN lists. Use when the user asks about listing quality, content compliance, or what specifically needs improvement.
  data: {
  // Per-ASIN Listing Quality Score blocks keyed by ASIN. Single mode carries exactly one entry (the main ASIN); multi mode carries one entry per session ASIN (main ASIN plus competitors). Each entry holds the ASIN's ``lqs`` plus its per-metric pass / fail breakdown (``metric_data``). Omitted (key absent) when no score is available.
  score_data?: { [key: string]: {
  // ASIN this score block describes.
  asin?: string | null;
  // 0–10 Listing Quality Score for this ASIN (a perfect score is emitted as the integer ``10``).
  lqs?: number | null;
  // Per-metric pass / fail breakdown keyed by the snake_cased metric id (e.g. ``title_length``, ``a_plus_content``) — typically 13 metrics covering content / media / reviews. Null when not available.
  metric_data?: { [key: string]: {
  // Stable metric id, e.g. ``titleLength``, ``aPlusContent`` (camelCase preserved — these are stable metric ids).
  metric?: string | null;
  // Upstream-defined grouping category (e.g. ``content``, ``media``, ``review``).
  metric_category?: string | null;
  // Human-readable metric label.
  metric_title?: string | null;
  // Tooltip / help copy describing the metric.
  metric_tooltip?: string | null;
  // Whether this ASIN passed the metric (pass/fail only; no numeric score).
  passed?: boolean | null;
}; } | null;
}; } | null;
  // Cross-ASIN aggregate score. Multi mode only — omitted (key absent) in single mode (no cross-ASIN aggregate). Carries ``average_lqs`` (mean LQS across session ASINs) and a per-metric ``metric_data`` map whose entries list the ASINs that passed / failed each metric.
  summary_data?: {
  // Average Listing Quality Score across the session ASINs (a perfect score is emitted as the integer ``10``).
  average_lqs?: number | null;
  // Per-metric cross-ASIN aggregate keyed by the snake_cased metric id; each entry lists ``asins_passed`` / ``asins_failed``.
  metric_data?: { [key: string]: {
  // ASINs in the session that failed this metric.
  asins_failed?: Array<string> | null;
  // ASINs in the session that passed this metric.
  asins_passed?: Array<string> | null;
  // Stable metric id, e.g. ``titleLength``, ``aPlusContent`` (camelCase preserved — these are stable metric ids).
  metric?: string | null;
  // Upstream-defined grouping category (e.g. ``content``, ``media``, ``review``).
  metric_category?: string | null;
  // Human-readable metric label.
  metric_title?: string | null;
  // Tooltip / help copy describing the metric.
  metric_tooltip?: string | null;
}; } | null;
} | null;
};
  // Always null — Listing Analyzer surfaces failures as an MCP error result (isError=true), not as an inline error payload.
  error: null;
}>>; };
```

## 62. `get_market_leaderboard`

Return compared leaderboard for one MT360 market: ranked categories, brands, or products by revenue and unit sales (totalValue, totalValueCompared, change, growth). Requires market_id, leader_board_tabs_type (0=CATEGORIES top-level only, 1=BRANDS, 2=PRODUCTS), date_from, date_to (yyyy-MM-dd; prefer list_user_markets defaultDateFrom/To), and sort (-totalValue top performing | -growth fastest growing | -change largest increase | change top declining). There is no category_id input; CATEGORIES is always the top-level ranking, not a subcategory drill-down. compared_date_from and compared_date_to are optional but must be passed together for the compare period (maps to DSA compareDateFrom/compareDateTo). If both are omitted, totalValueCompared, change, and growth are null. Passing only one is rejected. Dates are yyyy-MM-dd and inclusive. MT360 uses Saturday as the weekly cutoff: Mon–Fri the latest allowed end date is the Saturday of the week before last; Saturday–Sunday may use last Saturday. Each provided range must span at least 28 inclusive days. When a compare range is sent (compared_date_from + compared_date_to), the two ranges must be similar length (inclusive day counts differ by at most 5). change is an absolute amount delta, not a percent: on revenue rows it is currency change (scaled for display except Japan marketplaceId 10 and parentDataMode 1, same as totalValue); on unitSales rows it is unit-sales volume change (never /100). growth is the percent field, passed through from DSA with no processing (e.g. 26.873 means 26.873%) — never divide growth by 100. Values: totalValue/totalValueCompared on revenue rows are currency amounts already scaled for display except Japan and parent mode; unitSales row totals are not scaled. Nested item.price/priceAverage/priceDynamic/fbaFee/netPrice/pricePerUnit on revenue and unitSales are already /100 except Japan; listingScore is not scaled. item.fbaFee/pricePerUnit/price/priceAverage/sizeTier/yearSales/weight/height/width/length of -1 are returned as null (not /100). null and 0 are not scaled. estimatedRevenue, estimatedRevenueDynamic, estimatedSales, and estimatedSalesDynamic are omitted from item. Always pass parent_data_mode (false/0 = child/variation, true/1 = parent); omitted defaults to child. Prefer the market's parentDataMode from list_user_markets. Not for: headline share cards (get_market_statistics), time series (get_market_overall), or full product table (get_market_products). This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_market_leaderboard(args: {
  // Optional compare range start yyyy-MM-dd. Must be sent together with compared_date_to. If both are omitted, totalValueCompared, change, and growth are null. Dates are yyyy-MM-dd and inclusive. MT360 uses Saturday as the weekly cutoff: Mon–Fri the latest allowed end date is the Saturday of the week before last; Saturday–Sunday may use last Saturday. Each provided range must span at least 28 inclusive days. When a compare range is sent (compared_date_from + compared_date_to), the two ranges must be similar length (inclusive day counts differ by at most 5).
  compared_date_from?: string | null;
  // Optional compare range end yyyy-MM-dd. Must be sent together with compared_date_from. If both are omitted, totalValueCompared, change, and growth are null. Dates are yyyy-MM-dd and inclusive. MT360 uses Saturday as the weekly cutoff: Mon–Fri the latest allowed end date is the Saturday of the week before last; Saturday–Sunday may use last Saturday. Each provided range must span at least 28 inclusive days. When a compare range is sent (compared_date_from + compared_date_to), the two ranges must be similar length (inclusive day counts differ by at most 5).
  compared_date_to?: string | null;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Required primary range start yyyy-MM-dd. Must be on or before date_to. Dates are yyyy-MM-dd and inclusive. MT360 uses Saturday as the weekly cutoff: Mon–Fri the latest allowed end date is the Saturday of the week before last; Saturday–Sunday may use last Saturday. Each provided range must span at least 28 inclusive days.
  date_from: string;
  // Required primary range end yyyy-MM-dd. Must be on or after date_from. Dates are yyyy-MM-dd and inclusive. MT360 uses Saturday as the weekly cutoff: Mon–Fri the latest allowed end date is the Saturday of the week before last; Saturday–Sunday may use last Saturday. Each provided range must span at least 28 inclusive days.
  date_to: string;
  // Required tab. 0=CATEGORIES (top-level Amazon categories only; there is no category_id drill-down), 1=BRANDS, 2=PRODUCTS.
  leader_board_tabs_type: number;
  // Required. MT360/MI market id (from list_user_markets).
  market_id: number;
  // Parent vs child aggregation. Accepts boolean or integer: false/0 = child (variation) mode; true/1 = parent mode. If omitted or null, DSA defaults to child (0). Pass the market's parentDataMode from list_user_markets unless the user explicitly wants the other mode.
  parent_data_mode?: boolean | number | null;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Required. DSA LeaderBoardSortsV3Enum: -totalValue = top performing (highest primary value); -growth = fastest growing; -change = largest absolute increase; change = top declining (do not prefix with '-').
  sort: "-totalValue" | "-growth" | "-change" | "change";
}): Promise<CallToolResult<{
  // DSA market-tracker payload pass-through for statistics, leaderboard, overall, or current-data tools.
  data?: { [key: string]: unknown; } | Array<unknown> | null;
  error?: {
  // Error code string, e.g. 'INVALID_INPUT.PARAM_INVALID'.
  code: string;
  // Additional error context keyed by field or topic.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Suggested action to resolve the error.
  remediation: string;
} | null;
}>>; };
```

## 63. `get_market_products`

Return paginated current product rows for one MT360 market (ASIN attributes, BSR, brand, category, dynamics, etc.). Values: money fields (totalRevenue, totalRevenueActuals, price, priceDynamic, netPrice, pricePerUnit, fbaFee) are already scaled for display (/100) except Japan (marketplaceId 10); in parent mode (parentDataMode 1) totalRevenue is left at DSA scale. Null and 0 money values are not scaled. listingScore is not scaled. fbaFee, pricePerUnit, price, sizeTier, yearSales, weight, height, width, and length of -1 are returned as null (not /100). sizeTier is a DSA integer (not /100): 1=Small Standard-Size, 2=Large Standard-Size, 3=Small Oversize, 4=Medium Oversize, 5=Large Oversize, 6=Special Oversize, 7=Envelope, 8=Small and Light, 9=Small Letter, 10=Large Letter, 11=Extra Large Letter, 12=Small Envelope, 13=Standard Envelope, 14=Large Envelope, 15=Standard Parcel, 16=Small, 17=Standard Size, 18=Oversize, 19=Standard-Oversize, 20=Heavy and Bulky, 21=Supersize, 22=Extra Large Envelope, 23=Small Parcel. -1 is returned as null; other integers are pass-through. rating/reviews/packageQuantity are positive numbers or null; FBA is a seller-type label (AMZ/FBA/FBM/FLF/MERCH, or '-'). estimatedRevenue, estimatedRevenueDynamic, estimatedSales, and estimatedSalesDynamic are omitted. Requires market_id from list_user_markets plus date_from and date_to (yyyy-MM-dd; prefer list_user_markets defaultDateFrom/To). Dates are yyyy-MM-dd and inclusive. MT360 uses Saturday as the weekly cutoff: Mon–Fri the latest allowed end date is the Saturday of the week before last; Saturday–Sunday may use last Saturday. Each provided range must span at least 28 inclusive days. Keep per_page small (default 10, max 100); use sort (DSA whitelist enum; default -totalRevenue) and current_table_search to narrow results. Pagination: has_more is true when another page of rows exists. On the first request DSA computes total asynchronously to avoid timeouts, so total and last_page may be missing; use has_more (not total) to decide whether to fetch the next page. After the count job finishes, later responses include total and last_page. Always pass parent_data_mode (false/0 = child/variation, true/1 = parent); omitted defaults to child. Prefer the market's parentDataMode from list_user_markets. Not for: share summary (get_market_statistics), top-N leaders (get_market_leaderboard), or time-series overall (get_market_overall). This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_market_products(args: {
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Table search string.
  current_table_search?: string | null;
  // Required primary range start yyyy-MM-dd. Must be on or before date_to. Dates are yyyy-MM-dd and inclusive. MT360 uses Saturday as the weekly cutoff: Mon–Fri the latest allowed end date is the Saturday of the week before last; Saturday–Sunday may use last Saturday. Each provided range must span at least 28 inclusive days.
  date_from: string;
  // Required primary range end yyyy-MM-dd. Must be on or after date_from. Dates are yyyy-MM-dd and inclusive. MT360 uses Saturday as the weekly cutoff: Mon–Fri the latest allowed end date is the Saturday of the week before last; Saturday–Sunday may use last Saturday. Each provided range must span at least 28 inclusive days.
  date_to: string;
  // Required. MT360/MI market id (from list_user_markets).
  market_id: number;
  // 1-based page (default 1).
  page?: number | null;
  // Parent vs child aggregation. Accepts boolean or integer: false/0 = child (variation) mode; true/1 = parent mode. If omitted or null, DSA defaults to child (0). Pass the market's parentDataMode from list_user_markets unless the user explicitly wants the other mode.
  parent_data_mode?: boolean | number | null;
  // Rows per page (default 10, max 100).
  per_page?: number | null;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // DSA current-data sort. Prefix '-' for descending. Default -totalRevenue when omitted. Allowed fields: price, createdAt, brand, bestMonth, bbSeller, bsr, sellersQty, FBA, fbaFee, rating, reviews, reviewsDynamic, numberOfImages, sizeTier, height, width, length, weight, variationsCount, netPrice, yearSales, salesYearOverYear, salesChange, priceChange, salesToReviews, totalSales, totalSalesActuals, totalRevenue, totalRevenueActuals. Not asin, parentAsin, or estimatedRevenue.
  sort?: "price" | "-price" | "createdAt" | "-createdAt" | "brand" | "-brand" | "bestMonth" | "-bestMonth" | "bbSeller" | "-bbSeller" | "bsr" | "-bsr" | "sellersQty" | "-sellersQty" | "FBA" | "-FBA" | "fbaFee" | "-fbaFee" | "rating" | "-rating" | "reviews" | "-reviews" | "reviewsDynamic" | "-reviewsDynamic" | "numberOfImages" | "-numberOfImages" | "sizeTier" | "-sizeTier" | "height" | "-height" | "width" | "-width" | "length" | "-length" | "weight" | "-weight" | "variationsCount" | "-variationsCount" | "netPrice" | "-netPrice" | "yearSales" | "-yearSales" | "salesYearOverYear" | "-salesYearOverYear" | "salesChange" | "-salesChange" | "priceChange" | "-priceChange" | "salesToReviews" | "-salesToReviews" | "totalSales" | "-totalSales" | "totalSalesActuals" | "-totalSalesActuals" | "totalRevenue" | "-totalRevenue" | "totalRevenueActuals" | "-totalRevenueActuals";
}): Promise<CallToolResult<{
  // DSA market-tracker payload pass-through for statistics, leaderboard, overall, or current-data tools.
  data?: { [key: string]: unknown; } | Array<unknown> | null;
  error?: {
  // Error code string, e.g. 'INVALID_INPUT.PARAM_INVALID'.
  code: string;
  // Additional error context keyed by field or topic.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Suggested action to resolve the error.
  remediation: string;
} | null;
}>>; };
```

## 64. `get_market_statistics`

Return headline Market Performance statistics for one MT360 market: my share / revenue / units, total market revenue / units, brand mix, and optional compare-period fields (*Compared, *Change, *Dynamic). Snapshot for the selected date range — not a time series. Values: myMarketShare, myMarketShareCompared, myMarketShareDynamic, and each brands[].brandMarketShare are percent point numbers already (e.g. 12.5 means 12.5%), do not divide by 100. myMarketRevenueChange, myMarketSalesChange, totalRevenueChange, and totalSalesChange are also percent fields passed through from DSA with no scaling (e.g. 10.98 means 10.98%) — never divide them by 100. Revenue fields (myMarketRevenue*, totalRevenue*) are already scaled for display except Japan (marketplaceId 10), which is left at DSA scale. Requires market_id from list_user_markets plus date_from and date_to (yyyy-MM-dd; prefer list_user_markets defaultDateFrom/To). Dates are yyyy-MM-dd and inclusive. MT360 uses Saturday as the weekly cutoff: Mon–Fri the latest allowed end date is the Saturday of the week before last; Saturday–Sunday may use last Saturday. Each provided range must span at least 28 inclusive days. When a compare range is sent (compared_date_from + compared_date_to), the two ranges must be similar length (inclusive day counts differ by at most 5). There is no preset_id or saved-filter input; stats use the market as configured. Pass compared_date_from + compared_date_to together for period-over-period change. Always pass parent_data_mode (false/0 = child/variation, true/1 = parent); omitted defaults to child. Prefer the market's parentDataMode from list_user_markets. Not for: market list (list_user_markets), time series (get_market_overall), ranked leaders (get_market_leaderboard), or product rows (get_market_products). This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_market_statistics(args: {
  // Optional compare range start yyyy-MM-dd. Must be sent together with compared_date_to; omit both to skip the compare period. Dates are yyyy-MM-dd and inclusive. MT360 uses Saturday as the weekly cutoff: Mon–Fri the latest allowed end date is the Saturday of the week before last; Saturday–Sunday may use last Saturday. Each provided range must span at least 28 inclusive days. When a compare range is sent (compared_date_from + compared_date_to), the two ranges must be similar length (inclusive day counts differ by at most 5).
  compared_date_from?: string | null;
  // Optional compare range end yyyy-MM-dd. Must be sent together with compared_date_from; omit both to skip the compare period. Dates are yyyy-MM-dd and inclusive. MT360 uses Saturday as the weekly cutoff: Mon–Fri the latest allowed end date is the Saturday of the week before last; Saturday–Sunday may use last Saturday. Each provided range must span at least 28 inclusive days. When a compare range is sent (compared_date_from + compared_date_to), the two ranges must be similar length (inclusive day counts differ by at most 5).
  compared_date_to?: string | null;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Required primary range start yyyy-MM-dd. Must be on or before date_to. Dates are yyyy-MM-dd and inclusive. MT360 uses Saturday as the weekly cutoff: Mon–Fri the latest allowed end date is the Saturday of the week before last; Saturday–Sunday may use last Saturday. Each provided range must span at least 28 inclusive days.
  date_from: string;
  // Required primary range end yyyy-MM-dd. Must be on or after date_from. Dates are yyyy-MM-dd and inclusive. MT360 uses Saturday as the weekly cutoff: Mon–Fri the latest allowed end date is the Saturday of the week before last; Saturday–Sunday may use last Saturday. Each provided range must span at least 28 inclusive days.
  date_to: string;
  // Required. MT360/MI market id (from list_user_markets).
  market_id: number;
  // Parent vs child aggregation. Accepts boolean or integer: false/0 = child (variation) mode; true/1 = parent mode. If omitted or null, DSA defaults to child (0). Pass the market's parentDataMode from list_user_markets unless the user explicitly wants the other mode.
  parent_data_mode?: boolean | number | null;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{
  // DSA market-tracker payload pass-through for statistics, leaderboard, overall, or current-data tools.
  data?: { [key: string]: unknown; } | Array<unknown> | null;
  error?: {
  // Error code string, e.g. 'INVALID_INPUT.PARAM_INVALID'.
  code: string;
  // Additional error context keyed by field or topic.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Suggested action to resolve the error.
  remediation: string;
} | null;
}>>; };
```

## 65. `get_mcp_usage_info`

Returns the current caller's MCP tool-call remaining usage quota for the current period: the account id, the period limit, how many calls were used and how many remain. Calling this tool does not itself consume quota. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_mcp_usage_info(args: {
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{
  // Total tool-call quota for the current period.
  limit: number;
  // Calls remaining in the current period.
  remaining: number;
  // Calls consumed in the current period.
  used: number;
}>>; };
```

## 66. `get_monitoring_status`

Helium 10 Alerts — a seller's monitoring status. Omit asin for an account overview: total/used/available monitoring slots and the list of monitored ASINs (asins[]) with their marketplace, seller SKU and monitoring start date. Pass asin to get asin_rows[] — one row per marketplace the ASIN appears in, each with is_monitored, marketplace and seller SKU. Essential for distinguishing 'no changes' from 'not monitored' when other Alerts tools return empty. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_monitoring_status(args: {
  // Optional ASIN. Omit for the account overview; pass an ASIN to check whether it is currently monitored.
  asin?: string | null;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Helium 10 seller identifier.
  seller_id: string;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{
  // Monitoring status. Account mode (no asin) populates slot usage and Asins; ASIN mode populates is_monitored. Null on error.
  data: {
  // Per-marketplace monitoring rows for the queried ASIN (ASIN mode).
  asin_rows?: Array<{
  // Amazon ASIN.
  asin?: string | null;
  // Whether this ASIN is currently monitored in this marketplace.
  is_monitored?: boolean | null;
  // Marketplace code, e.g. US.
  marketplace?: string | null;
  // Monitoring start date, YYYY-MM-DD.
  monitoring_since?: string | null;
  // Seller SKU for this ASIN.
  seller_sku?: string | null;
}> | null;
  // Monitored ASINs (account mode).
  asins?: Array<{
  // Amazon ASIN.
  asin?: string | null;
  // Marketplace code, e.g. US.
  marketplace?: string | null;
  // Monitoring start date, YYYY-MM-DD.
  monitoring_since?: string | null;
  // Seller SKU for this ASIN.
  seller_sku?: string | null;
}> | null;
  // Remaining slots; null when total is unavailable.
  available_slots?: number | null;
  // Total monitoring slots allowed by the plan; null if unavailable.
  total_slots?: number | null;
  // Slots currently in use (monitored products).
  used_slots?: number | null;
} | null;
  error: null;
} | {
  data: null;
  // Structured error response from io-api.
  error: {
  // MCP string error code.
  code: string;
  // Optional structured error details.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Optional human-readable remediation guidance (may be omitted).
  remediation?: string;
};
}>>; };
```

## 67. `get_my_amazon_seller_accounts`

What: Return the caller's bound Amazon seller accounts (one row per SP-API token / seller binding). Each row includes seller_id, marketplace_list (short country codes), region (NA / EU / FE), status (whether the binding is still valid), optional alias, and created_at (UTC bind time). No required input — default returns all bindings (active and inactive). Optional status filter narrows to valid-only or invalid-only.

When to use: User (or agent) needs to know which seller IDs and marketplaces are available for this account before calling other Commerce tools; wants to list / audit linked Amazon seller accounts; needs to distinguish active vs revoked / invalid tokens; wants a human-readable alias when present.

When NOT to use:

Brand Registry / SQA seller × marketplace picker → get_brand_seller_profiles (ResearchMCP). Use that tool when the next call is Brand-Registry–gated (e.g. get_search_query_performance, ABA Brand Analytics), or the user only needs a Brand-Registered seller_id + one marketplace from marketplaces[]. It filters out non–Brand-Registered and does not return region / status / alias / created_at, and its marketplace enum is narrower (US/UK/DE/JP/CA/FR/IT/ES). Do not use this Commerce tool as a substitute for that picker, and do not use get_brand_seller_profiles when you need the full SP-API binding roster for P&L / inventory / COGS / Indirect Costs.

Product catalog / SKU browse → list_my_products (HP-1267)

P&L, inventory, COGS, or Indirect Costs metrics / CRUD → the dedicated tools for those domains

Discovering Amazon sellers that are not linked to this H10 account

Guidance: Call with no arguments first to discover the full set of seller_id + marketplace_list values, then pass those into downstream Commerce tools' seller_ids / marketplace filters. Prefer status=true when the user only wants currently usable sellers. Do not invent seller IDs or marketplaces. If the task is specifically SQA / Brand Analytics and Brand Registry eligibility matters, prefer get_brand_seller_profiles instead. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_my_amazon_seller_accounts(args: {
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Filter by binding validity. Omit / null (default) = return ALL sellers (active + inactive). true = only valid/active bindings. false = only invalid/inactive bindings.
  status?: boolean | null;
}): Promise<CallToolResult<{
  // The caller's bound Amazon seller accounts. Never null in practice; an account with no bindings returns rows: [].
  data: {
  // One row per bound Amazon seller account. Empty list when the account has no bindings matching the status filter.
  rows: Array<{
  // Optional display name set in our system. Omitted when empty/null.
  alias?: string | null;
  // UTC timestamp when this seller account was bound, e.g. '2026-05-20 06:14:29'. Omitted until the upstream field is populated.
  created_at?: string | null;
  // Amazon short country codes for the marketplaces linked to this seller, e.g. ['BR', 'MX', 'US']. Sorted ascending. Same value space as the shared 'marketplace' filter. Empty only when upstream returns no marketplaces.
  marketplace_list: Array<string>;
  // Amazon SP-API region for this seller token: 'NA', 'EU', or 'FE'.
  region?: string | null;
  // Amazon seller ID string, e.g. 'A2R7UX9BFSMS61'. Same value space as the seller_ids filter on other Commerce tools.
  seller_id: string;
  // Whether the binding is currently valid/usable. true = active; false = invalid / revoked / unusable.
  status: boolean;
}>;
} | null;
  // Null on success; error object on failure.
  error?: {
  // Error code string, e.g. 'INVALID_INPUT.DATE_RANGE_INVALID'.
  code: string;
  // Additional error context keyed by field or topic.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Suggested action to resolve the error.
  remediation: string;
} | null;
  // Optional non-blocking advisory (spec §6.1). Present only when the platform attaches one — currently a quota near-limit reminder (code QUOTA_NEAR_LIMIT). Null/absent on the vast majority of responses. Independent of 'error': the call still succeeded.
  notice?: {
  // Notice code, e.g. 'QUOTA_NEAR_LIMIT'.
  code: string;
  // Human-readable advisory message for the end user.
  message: string;
  // Advisory severity, e.g. 'warning'.
  severity: string;
} | null;
}>>; };
```

## 68. `get_my_wmt_seller_accounts`

What: Return the caller's bound WALMART seller accounts (one row per Walmart auth-token binding). Each row includes seller_id (Walmart partner id — the same value space as seller_ids on the other Walmart Commerce tools), marketplace_list (day-1 always ['US']), status (whether the binding is still valid), optional alias, and created_at (UTC bind time, ISO-8601 with 'Z'). No required input — default returns all bindings (active and inactive). Optional status filter narrows to valid-only or invalid-only. There is no region field for Walmart.

When to use: User (or agent) needs to know which Walmart seller IDs (partner ids) and marketplaces are available for this account before calling other Walmart Commerce tools; wants to list / audit linked Walmart seller accounts; needs to distinguish active vs revoked / invalid tokens; wants a human-readable alias when present.

When NOT to use:

Amazon seller discovery → get_my_amazon_seller_accounts

Amazon Brand Registry / SQA picker → get_brand_seller_profiles (ResearchMCP; Amazon-only)

Walmart P&L / inventory / product metrics → the dedicated Walmart Commerce tools for those domains

Walmart Ads profile / shop-to-ads-account mapping → Ads MCP (not this Commerce roster)

Discovering Walmart sellers that are not linked to this H10 account

Guidance: Call with no arguments first to discover the full set of seller_id + marketplace_list values, then pass those into downstream Walmart tools' seller_ids / marketplace filters. Prefer status=true when the user only wants currently usable sellers. Do not invent seller IDs or marketplaces. Do not mix Walmart seller_id values with Amazon seller IDs. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_my_wmt_seller_accounts(args: {
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Filter by binding validity. Omit / null (default) = return ALL Walmart sellers (active + inactive). true = only valid/active bindings. false = only invalid/inactive bindings. Must be a JSON boolean — 1 / 0 are INVALID_INPUT, not shorthand for true / false.
  status?: boolean | null;
}): Promise<CallToolResult<{
  // The caller's bound Walmart seller accounts. Never null in practice; an account with no bindings returns rows: [].
  data: {
  // One row per bound Walmart seller account. Empty list when the account has no bindings matching the status filter.
  rows: Array<{
  // Optional display name set in our system. Omitted when empty/null.
  alias?: string | null;
  // UTC timestamp when this seller account was bound, ISO-8601 with 'Z', e.g. '2026-07-28T11:08:24Z'. The value is UTC. Omitted when upstream does not provide it.
  created_at?: string | null;
  // Walmart marketplace short country codes for this seller. Day-1 always ['US']; aligns with the Walmart tools' marketplace filter.
  marketplace_list: Array<string>;
  // Walmart partner id, e.g. '10003176407'. Same value space as the seller_ids filter on the Walmart Commerce tools. NOT an Amazon seller ID.
  seller_id: string;
  // Whether the binding is currently valid/usable. true = active; false = invalid / revoked / unusable.
  status: boolean;
}>;
} | null;
  // Null on success; error object on failure.
  error?: {
  // Error code string, e.g. 'INVALID_INPUT.DATE_RANGE_INVALID'.
  code: string;
  // Additional error context keyed by field or topic.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Suggested action to resolve the error.
  remediation: string;
} | null;
  // Optional non-blocking advisory (spec §6.1). Present only when the platform attaches one — currently a quota near-limit reminder (code QUOTA_NEAR_LIMIT). Null/absent on the vast majority of responses. Independent of 'error': the call still succeeded.
  notice?: {
  // Notice code, e.g. 'QUOTA_NEAR_LIMIT'.
  code: string;
  // Human-readable advisory message for the end user.
  message: string;
  // Advisory severity, e.g. 'warning'.
  severity: string;
} | null;
}>>; };
```

## 69. `get_product_live_status`

Helium 10 Alerts — current attribute snapshot for one ASIN: price, FBA fee, Buy Box owner, number of sellers, listing status (active/suppressed/inactive), is_adult_flagged, has_returned_tag (Frequently Returned Item badge), category, brand, main image, package dimensions (size_tier) and last-updated time. Answers 'what is it now?' (current state), as opposed to search_alert_events ('what changed'). The ASIN must have monitoring enabled; querying an unmonitored ASIN returns an INVALID_INPUT error. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_product_live_status(args: {
  // Amazon ASIN to query, 10-char alphanumeric (e.g. B01LR5S6HK).
  asin: string;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Marketplace code: US, UK, DE, JP, etc.
  marketplace: "US" | "CA" | "MX" | "DE" | "ES" | "IT" | "FR" | "UK" | "IN" | "NL" | "AU" | "JP" | "AE" | "BR" | "SA";
  // Helium 10 seller identifier.
  seller_id: string;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{
  // Current-state snapshot for a single ASIN. Null on error.
  data: {
  // Amazon ASIN.
  asin?: string | null;
  // Brand name.
  brand?: string | null;
  // Whether the seller holds the Buy Box; null when there is no Buy Box winner data yet (not the same as false).
  buybox_is_mine?: boolean | null;
  // Buy Box holder seller ID; null when there is no Buy Box winner data yet — do not report a winner in that case.
  buybox_owner?: string | null;
  // Current product category.
  category?: string | null;
  // Current FBA fulfillment fee with currency; null when unavailable.
  fba_fee?: string | null;
  // Whether the listing carries Amazon's 'Frequently Returned Item' badge; null when unknown.
  has_returned_tag?: boolean | null;
  // Whether the listing is flagged as adult content.
  is_adult_flagged?: boolean | null;
  // Snapshot last-updated time, ISO 8601 UTC instant, e.g. '2026-07-29T08:35:06Z'.
  last_updated?: string | null;
  // active / suppressed / inactive.
  listing_status?: string | null;
  // Main image URL.
  main_image_url?: string | null;
  // Number of competing sellers; null when no offer data has been scraped yet.
  number_of_sellers?: number | null;
  // Current price with currency, e.g. '39.97 USD'; null when no price has been scraped yet.
  price?: string | null;
  // Package dimensions, e.g. {"Width":11,"Height":4.25,"Length":18.5,"Weight":4.3497}; null when package dimensions are unavailable.
  size_tier?: { [key: string]: unknown; } | null;
  // Current product title.
  title?: string | null;
} | null;
  error: null;
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
} | {
  data: null;
  // Structured error response from io-api.
  error: {
  // MCP string error code.
  code: string;
  // Optional structured error details.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Optional human-readable remediation guidance (may be omitted).
  remediation?: string;
};
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
}>>; };
```

## 70. `get_product_profit_and_loss_breakdown`

Helium 10 Product P&L Breakdown (formerly Profits). Returns per-product breakdown snapshots in rows — product info plus nested metrics per requested breakdown key, showing which fee type, ad channel, or cost component drives each metric per product. Use to diagnose why a specific ASIN has unexpectedly high costs, compare fee/ad-spend breakdowns across products, and find the most favorable cost structures. Not for: per-product Level 1 only (use get_product_profit_and_loss_summary); account breakdown (use get_account_profit_and_loss_breakdown); breakdown trends (use get_product_profit_and_loss_breakdown_series). A cost_of_goods or indirect_cost node of 0 may mean the cost was never configured rather than genuinely zero — verify with list_amazon_cogs / list_amazon_indirect_cost_settings before treating the cost picture as complete.Comparing two periods? Both must span the same number of days: read data.meta.period_days from each response and require them to match. A 30-day window against a 31-day one inflates every percentage delta by roughly 2pp. If they differ, align the windows, switch to daily averages, or state the difference and its effect on every percentage you report.Amounts are converted into the requested currency per marketplace before aggregation; omitting marketplace means the figures are the combined total across every authorised marketplace, so say so when reporting them. data.meta echoes both. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_product_profit_and_loss_breakdown(args: { asins?: Array<string> | null; breakdown_metrics?: Array<string> | null; context?: string; currency?: "AED" | "AUD" | "BRL" | "CAD" | "CNY" | "EGP" | "EUR" | "GBP" | "INR" | "JPY" | "MXN" | "PLN" | "SAR" | "SEK" | "SGD" | "TRY" | "USD" | "ZAR"; current_date_from: string; current_date_to: string; date_tz?: "Africa/Cairo" | "Africa/Johannesburg" | "America/Los_Angeles" | "America/Sao_Paulo" | "Asia/Dubai" | "Asia/Kolkata" | "Asia/Riyadh" | "Asia/Shanghai" | "Asia/Singapore" | "Asia/Tokyo" | "Australia/Sydney" | "Europe/Amsterdam" | "Europe/Brussels" | "Europe/Dublin" | "Europe/Istanbul" | "Europe/London" | "Europe/Paris" | "Europe/Stockholm" | "Europe/Warsaw"; marketplace?: Array<string> | null; page_index?: number | null; page_size?: number | null; product_level?: "asin" | "sku" | "parent_asin"; seller_ids?: Array<string> | null; session_id?: string; skus?: Array<string> | null; sort_by?: "sales" | "units_sold" | "refund" | "advertising_cost" | "amazon_fees" | "reimbursements" | "cost_of_goods" | "indirect_cost" | "gross_revenue" | "sessions" | "units_returned"; sort_order?: "asc" | "desc" | null; }): Promise<CallToolResult>; };
```

## 71. `get_product_profit_and_loss_breakdown_series`

Helium 10 Product P&L Breakdown Trends (formerly Profits). Returns a per-product breakdown time series (nested values on children) — the largest Phase 1 payload. Use when you need product-level granularity, fee/cost-component drill-down, and over-time trends all at once — e.g. how a specific ASIN's FBA fee changed month over month. Keep breakdown_metrics, filters, and page_size minimal. Not for: a snapshot (use get_product_profit_and_loss_breakdown); Level 1 trends only (use get_product_profit_and_loss_summary_series); account breakdown trends (use get_account_profit_and_loss_breakdown_series). A cost_of_goods or indirect_cost node of 0 may mean the cost was never configured rather than genuinely zero — verify with list_amazon_cogs / list_amazon_indirect_cost_settings before treating the cost picture as complete.Comparing two periods? Both must span the same number of days: read data.meta.period_days from each response and require them to match. A 30-day window against a 31-day one inflates every percentage delta by roughly 2pp. If they differ, align the windows, switch to daily averages, or state the difference and its effect on every percentage you report.Amounts are converted into the requested currency per marketplace before aggregation; omitting marketplace means the figures are the combined total across every authorised marketplace, so say so when reporting them. data.meta echoes both. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_product_profit_and_loss_breakdown_series(args: { asins?: Array<string> | null; breakdown_metrics?: Array<string> | null; context?: string; currency?: "AED" | "AUD" | "BRL" | "CAD" | "CNY" | "EGP" | "EUR" | "GBP" | "INR" | "JPY" | "MXN" | "PLN" | "SAR" | "SEK" | "SGD" | "TRY" | "USD" | "ZAR"; current_date_from: string; current_date_to: string; date_tz?: "Africa/Cairo" | "Africa/Johannesburg" | "America/Los_Angeles" | "America/Sao_Paulo" | "Asia/Dubai" | "Asia/Kolkata" | "Asia/Riyadh" | "Asia/Shanghai" | "Asia/Singapore" | "Asia/Tokyo" | "Australia/Sydney" | "Europe/Amsterdam" | "Europe/Brussels" | "Europe/Dublin" | "Europe/Istanbul" | "Europe/London" | "Europe/Paris" | "Europe/Stockholm" | "Europe/Warsaw"; granularity: "day" | "week" | "month"; marketplace?: Array<string> | null; page_index?: number | null; page_size?: number | null; product_level?: "asin" | "sku" | "parent_asin"; seller_ids?: Array<string> | null; session_id?: string; skus?: Array<string> | null; sort_by?: "sales" | "units_sold" | "refund" | "advertising_cost" | "amazon_fees" | "reimbursements" | "cost_of_goods" | "indirect_cost" | "gross_revenue" | "sessions" | "units_returned"; sort_order?: "asc" | "desc" | null; }): Promise<CallToolResult>; };
```

## 72. `get_product_profit_and_loss_summary`

Helium 10 Product P&L Summary (formerly Profits). Returns product-level P&L SNAPSHOTS — one row per product_level (sku | asin | parent_asin) with identity fields and a single Level 1 metrics map covering the WHOLE date range; NO time buckets, no trend, no breakdown trees. Use to rank products by profitability for one period, compare margin across the catalog, flag loss-making SKUs, build a product-level report. IMPORTANT: if the question mentions a TREND or change OVER TIME (daily/weekly, 'over the last N days', 'units sold trend', etc.) use get_product_profit_and_loss_summary_series instead — it ranks top-N AND returns the time series in one call. Not for: store-wide totals (use get_account_profit_and_loss_summary); per-product trends (use get_product_profit_and_loss_summary_series); per-product drill-down (use get_product_profit_and_loss_breakdown). A cost_of_goods or indirect_cost of 0 on any row may mean the cost was never configured rather than genuinely zero — data.caveats flags that when it happens. Verify with list_amazon_cogs / list_amazon_indirect_cost_settings before describing the figures as normal: net_profit and margin are upper bounds until you do.Comparing two periods? Both must span the same number of days: read data.meta.period_days from each response and require them to match. A 30-day window against a 31-day one inflates every percentage delta by roughly 2pp. If they differ, align the windows, switch to daily averages, or state the difference and its effect on every percentage you report.Amounts are converted into the requested currency per marketplace before aggregation; omitting marketplace means the figures are the combined total across every authorised marketplace, so say so when reporting them. data.meta echoes both. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_product_profit_and_loss_summary(args: { asins?: Array<string> | null; context?: string; currency?: "AED" | "AUD" | "BRL" | "CAD" | "CNY" | "EGP" | "EUR" | "GBP" | "INR" | "JPY" | "MXN" | "PLN" | "SAR" | "SEK" | "SGD" | "TRY" | "USD" | "ZAR"; current_date_from: string; current_date_to: string; date_tz?: "Africa/Cairo" | "Africa/Johannesburg" | "America/Los_Angeles" | "America/Sao_Paulo" | "Asia/Dubai" | "Asia/Kolkata" | "Asia/Riyadh" | "Asia/Shanghai" | "Asia/Singapore" | "Asia/Tokyo" | "Australia/Sydney" | "Europe/Amsterdam" | "Europe/Brussels" | "Europe/Dublin" | "Europe/Istanbul" | "Europe/London" | "Europe/Paris" | "Europe/Stockholm" | "Europe/Warsaw"; marketplace?: Array<string> | null; page_index?: number | null; page_size?: number | null; product_level?: "asin" | "sku" | "parent_asin"; seller_ids?: Array<string> | null; session_id?: string; skus?: Array<string> | null; sort_by?: "sales" | "units_sold" | "units_returned" | "refund" | "promo" | "advertising_cost" | "amazon_fees" | "reimbursements" | "estimated_payout" | "cost_of_goods" | "gross_revenue" | "gross_profit" | "expenses" | "indirect_cost" | "net_profit" | "margin" | "roi" | "tacos" | "ads_acos" | "refunds_percentage" | "sessions"; sort_order?: "asc" | "desc" | null; }): Promise<CallToolResult>; };
```

## 73. `get_product_profit_and_loss_summary_series`

Helium 10 Product P&L Trends (formerly Profits). Returns per-product Level 1 P&L TIME SERIES — each row carries product info plus metric_series (metric_key, values) bucketed by granularity (day | week | month). THIS is the tool whenever the question asks how a metric moves OVER TIME — trend, trajectory, 'over the last N days', daily/weekly, 'units sold trend', 'revenue over time', 'net profit trend'. It ALSO ranks: sort_by + page_size return the top-N products (e.g. top 10 by net_profit) EACH with its metric_series, so 'top-N products' TREND' is a single call here (do NOT use the snapshot tool for that). No breakdown trees. Not for: a point-in-time snapshot with no time buckets (use get_product_profit_and_loss_summary); account trends (use get_account_profit_and_loss_summary_series); breakdown trends (use get_product_profit_and_loss_breakdown_series). A cost_of_goods or indirect_cost series that is 0 in every bucket may mean the cost was never configured rather than genuinely zero — data.caveats flags that when it happens. Verify with list_amazon_cogs / list_amazon_indirect_cost_settings before describing the figures as normal: net_profit and margin are upper bounds until you do.Comparing two periods? Both must span the same number of days: read data.meta.period_days from each response and require them to match. A 30-day window against a 31-day one inflates every percentage delta by roughly 2pp. If they differ, align the windows, switch to daily averages, or state the difference and its effect on every percentage you report.Amounts are converted into the requested currency per marketplace before aggregation; omitting marketplace means the figures are the combined total across every authorised marketplace, so say so when reporting them. data.meta echoes both. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_product_profit_and_loss_summary_series(args: { asins?: Array<string> | null; context?: string; currency?: "AED" | "AUD" | "BRL" | "CAD" | "CNY" | "EGP" | "EUR" | "GBP" | "INR" | "JPY" | "MXN" | "PLN" | "SAR" | "SEK" | "SGD" | "TRY" | "USD" | "ZAR"; current_date_from: string; current_date_to: string; date_tz?: "Africa/Cairo" | "Africa/Johannesburg" | "America/Los_Angeles" | "America/Sao_Paulo" | "Asia/Dubai" | "Asia/Kolkata" | "Asia/Riyadh" | "Asia/Shanghai" | "Asia/Singapore" | "Asia/Tokyo" | "Australia/Sydney" | "Europe/Amsterdam" | "Europe/Brussels" | "Europe/Dublin" | "Europe/Istanbul" | "Europe/London" | "Europe/Paris" | "Europe/Stockholm" | "Europe/Warsaw"; granularity: "day" | "week" | "month"; marketplace?: Array<string> | null; page_index?: number | null; page_size?: number | null; product_level?: "asin" | "sku" | "parent_asin"; seller_ids?: Array<string> | null; session_id?: string; skus?: Array<string> | null; sort_by?: "sales" | "units_sold" | "units_returned" | "refund" | "promo" | "advertising_cost" | "amazon_fees" | "reimbursements" | "estimated_payout" | "cost_of_goods" | "gross_revenue" | "gross_profit" | "expenses" | "indirect_cost" | "net_profit" | "margin" | "roi" | "tacos" | "ads_acos" | "refunds_percentage" | "sessions"; sort_order?: "asc" | "desc" | null; }): Promise<CallToolResult>; };
```

## 74. `get_sales_velocity`

Return units sold over time (sales velocity) per product row, bucketed by day / week / month over an inclusive date range. Each row includes product information (including fulfillment_type). Not a single snapshot; not revenue, fees, or other P&L metrics. Use when the user asks how units sold trend, compare periods, velocity by SKU/ASIN, or needs time-bucketed unit counts for inventory / restock planning. Not for: snapshot on-hand / inbound quantities (use get_inventory_values); FBA health / aging (use get_fba_inventory_health_status); full P&L trends (use get_product_profit_and_loss_summary_series or get_account_profit_and_loss_summary_series). Date range and granularity are OPTIONAL: omit BOTH current_date_from and current_date_to to default to the most recent 30 calendar days (in date_tz), and omit granularity to default to daily buckets — so 'recent sales velocity' needs no date/granularity args. Providing only one date endpoint is an error. Narrow with seller_ids, marketplace, asins, skus, fulfillment_type, and pagination. Prefer this over product P&L series when only units sold matter (smaller payload). This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_sales_velocity(args: {
  // Optional. SQL IN filter by ASIN list (array of strings, e.g. ['B001234567']). Default null (all ASINs).
  asins?: Array<string> | null;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Optional inclusive start day in date_tz, YYYY-MM-DD (no time). Omit BOTH current_date_from and current_date_to to default to the most recent 30 calendar days (current_date_to = today in date_tz, current_date_from = today - 29). Providing only one of the two endpoints is an error (INVALID_INPUT).
  current_date_from?: string | null;
  // Optional inclusive end day in date_tz, YYYY-MM-DD (no time). Omit BOTH endpoints to default to the most recent 30 calendar days. Providing only one of the two endpoints is an error (INVALID_INPUT). A value after today in date_tz is silently clamped to today in date_tz (the future has no data).
  current_date_to?: string | null;
  // Timezone used to interpret calendar dates. Must be one of the Amazon marketplace report timezones (not any IANA timezone). Examples: America/Los_Angeles, Europe/Paris, Europe/London. Italy/Germany/Spain use Europe/Paris (not Europe/Rome or Europe/Berlin). Do not pass a city name, abbreviation, or UTC offset. Optional; when omitted, defaults to the queried marketplace's report timezone (America/Los_Angeles when no single marketplace is set).
  date_tz?: "Africa/Cairo" | "Africa/Johannesburg" | "America/Los_Angeles" | "America/Sao_Paulo" | "Asia/Dubai" | "Asia/Kolkata" | "Asia/Riyadh" | "Asia/Shanghai" | "Asia/Singapore" | "Asia/Tokyo" | "Australia/Sydney" | "Europe/Amsterdam" | "Europe/Brussels" | "Europe/Dublin" | "Europe/Istanbul" | "Europe/London" | "Europe/Paris" | "Europe/Stockholm" | "Europe/Warsaw";
  // Optional fulfillment channel filter: FBA | FBM. Omit to query both FBA and FBM inventory.
  fulfillment_type?: "FBA" | "FBM" | null;
  // Optional time bucket size: day | week | month. Omit to default to 'day'. Week/month align to calendar boundaries (Mon start / 1st); first/last buckets may be partial.
  granularity?: "day" | "week" | "month";
  // Optional marketplace filter as an array of region short codes ONLY (e.g. ['US', 'CA', 'MX']); Amazon SP-API marketplace IDs (e.g. 'ATVPDKIKX0DER'), seller IDs, and domains are rejected. Allowed: AE, AU, BE, BR, CA, CN, DE, EG, ES, FR, IE, IN, IT, JP, MX, NL, PL, SA, SE, SG, TR, UK, US, ZA. Default null (all authorised marketplaces).
  marketplace?: Array<string> | null;
  // Optional. Page number (integer), starts from 1. Default 1.
  page_index?: number | null;
  // Optional. Rows per page (integer). Min 5, max 1000. Default 50.
  page_size?: number | null;
  // Optional. SQL IN filter on seller account IDs (array of strings). Profits intersects with sellers from Pacvue token; no intersection → empty result. Default null (all authorised sellers).
  seller_ids?: Array<string> | null;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Optional. SQL IN filter by seller SKU list (array of strings). Default null (all SKUs).
  skus?: Array<string> | null;
}): Promise<CallToolResult<{
  // Paginated product-level sales velocity (units sold) time series. Null when no matching data exists.
  data: {
  // 1-based page number that was returned. Matches the page_index request parameter (defaults to 1 when not specified).
  page_index: number;
  // Maximum number of rows per page that was applied. Matches the page_size request parameter (defaults to 50 when not specified; range 5–1000).
  page_size: number;
  // Product rows for the current page, each containing product identity (asin, sku, product_name, brand, image_url, marketplace, seller_id, fulfillment_type) and a sales_velocity time series. Empty list when no products match the filters.
  rows: Array<{
  // Amazon Standard Identification Number (ASIN) of the child product variant, e.g. 'B001234567'. Null when the product level is parent_asin.
  asin?: string | null;
  // Brand name associated with the product as registered in Amazon Brand Registry or the seller's catalog. Null when not available.
  brand?: string | null;
  // Fulfillment channel for this row: 'FBA' (Fulfilled by Amazon) or 'FBM' (Fulfilled by Merchant). Determines which inventory metrics are present on the row.
  fulfillment_type?: string | null;
  // HTTPS URL of the main product image thumbnail served by Amazon. Null when the product has no image or metadata is unavailable.
  image_url?: string | null;
  // Amazon marketplace short country code where this product is sold (e.g. 'US', 'CA', 'UK'). Same vocabulary as the 'marketplace' filter parameter — this value can be fed straight back as a filter.
  marketplace?: string | null;
  // Parent ASIN that groups child variants (e.g. different sizes/colours). Populated at all product levels; null when not applicable.
  parent_asin?: string | null;
  // Amazon product listing title as it appears on the detail page. May be null if the product metadata is not available.
  product_name?: string | null;
  // Units-sold time series for this product, bucketed by the requested granularity. Single object (not an array) — only units_sold, not the other 20 P&L metrics.
  sales_velocity: {
  // Map of bucket-start date (YYYY-MM-DD in date_tz) to units sold in that bucket. Non-negative integer semantics; wire value may be float (e.g. 14.0). Null = unavailable data point for that bucket.
  values: { [key: string]: number | number | null; };
};
  // Amazon seller account ID (merchant token) that owns this product listing, e.g. 'A2EUQ1WTGCTBG2'. Corresponds to the seller_ids filter parameter.
  seller_id?: string | null;
  // Seller-defined SKU, unique within a seller account. Reliably populated when product_level=sku. At asin/parent_asin level: typically null in non-series tools, but may be non-null in *_series tools; use asin/parent_asin for identity matching.
  sku?: string | null;
}>;
  // Total number of matching product rows across all pages for the given filters. Use this value together with page_size to determine the total number of pages: ceil(total_count / page_size).
  total_count: number;
} | null;
  // Null on success; error object on failure.
  error?: {
  // Error code string, e.g. 'INVALID_INPUT.DATE_RANGE_INVALID'.
  code: string;
  // Additional error context keyed by field or topic.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Suggested action to resolve the error.
  remediation: string;
} | null;
  // Optional non-blocking advisory (spec §6.1). Present only when the platform attaches one — currently a quota near-limit reminder (code QUOTA_NEAR_LIMIT). Null/absent on the vast majority of responses. Independent of 'error': the call still succeeded.
  notice?: {
  // Notice code, e.g. 'QUOTA_NEAR_LIMIT'.
  code: string;
  // Human-readable advisory message for the end user.
  message: string;
  // Advisory severity, e.g. 'warning'.
  severity: string;
} | null;
}>>; };
```

## 75. `get_search_query_performance`

Helium 10 Search Query Analyzer (SQA). Reads Amazon Search Query Performance (SQP) funnel data for 1–20 Brand-Registered ASINs over 1–12 weekly or monthly periods: a summary header (16 aggregate metrics, always over the FULL query result) plus a per-query keywords[] table (45 fields covering the impression→click→cart→purchase funnel, your share at each stage, PPC, ABA shares, and CPR), sorted by search volume descending. Use for conversion-truth diagnostics vs the market — where you lose CTR/CVR, which queries drag you down, how much purchase share you leave on the table, and WoW/MoM funnel shifts. Returns up to `limit` rows per call (default 50, max 10000); one page inlines at most ~500 rows / ~1MB, and requests far above that (>1000 rows or >~2MB) are delivered as a download link instead of inline rows. If data.pagination.has_more is true the result is incomplete: call again with `cursor` = data.pagination.next_cursor, but prefer narrowing filters over paginating through everything. Cursors expire 30 minutes after being issued and are bound to the query that issued them. Set response_format='download' when the user explicitly wants the data as a file. REQUIRES Brand Registry. Needs (seller_id, marketplace) — if the user names neither, call get_brand_seller_profiles first. Not for: reverse keyword search on one ASIN (use get_keywords_by_asin); scoring a finalized keyword list (use analyze_keywords). This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_search_query_performance(args: { asin: Array<string>; context?: string; cursor?: string | null; export_format?: "json" | "csv"; fields?: Array<string> | null; limit?: number; marketplace: "US" | "CA" | "MX" | "BR" | "DE" | "ES" | "FR" | "IT" | "UK" | "NL" | "TR" | "IN" | "AE" | "EG" | "SA" | "SE" | "PL" | "JP" | "AU" | "SG" | "BE" | "ZA" | "IE"; response_format?: "auto" | "inline" | "download"; seller_id: string; session_id?: string; time_dimension: "week" | "month"; time_period: Array<string>; }): Promise<CallToolResult>; };
```

## 76. `get_seller_feedback`

Helium 10 Alerts — Brand or seller feedback rating and recent negative reviews, for reputation monitoring and competitor assessment. seller_id may be the caller's own seller or a competitor's. For an account-level rollup that also surfaces feedback notifications alongside other alert types, see get_change_rollup. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_seller_feedback(args: {
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Marketplace code: US, UK, DE, JP, etc.
  marketplace: "US" | "CA" | "MX" | "DE" | "ES" | "IT" | "FR" | "UK" | "IN" | "NL" | "AU" | "JP" | "AE" | "BR" | "SA";
  // Target seller ID; can be the caller's own seller or a competitor's.
  seller_id: string;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Feedback lookback window. Defaults to 90d.
  time_window?: "30d" | "90d" | "365d" | "lifetime";
}): Promise<CallToolResult<{
  // Seller feedback summary. Null on error.
  data: {
  // Negative feedback rate, 0.0-1.0.
  negative_percentage?: number | null;
  // Neutral feedback rate, 0.0-1.0.
  neutral_percentage?: number | null;
  // Overall rating out of 5, e.g. 4.7.
  overall_rating?: number | null;
  // Positive feedback rate, 0.0-1.0.
  positive_percentage?: number | null;
  // Negative feedback from the last 30 days, most recent first, max 5 items.
  recent_negative?: Array<{
  // Negative feedback content.
  comment?: string | null;
  // Negative feedback date, format YYYY-MM-DD.
  date?: string | null;
}> | null;
  // Target seller ID.
  seller_id?: string | null;
  // Total number of feedback entries.
  total_feedback_count?: number | null;
} | null;
  error: null;
} | {
  data: null;
  // Structured error response from io-api.
  error: {
  // MCP string error code.
  code: string;
  // Optional structured error details.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Optional human-readable remediation guidance (may be omitted).
  remediation?: string;
};
}>>; };
```

## 77. `get_size_tier_suggestions`

Helium 10 Alerts — FBA (Fulfilled by Amazon) Size Tier optimization suggestions for a brand's monitored ASINs: which products could drop to a cheaper Size Tier by adjusting packaging dimensions, and the estimated FBA fee savings. Omit asin for all monitored products in the marketplace; pass asin for a single product. Returns an empty suggestions list when no optimization applies. For a one-off historical event of when this suggestion appeared, see search_alert_events (alert type SIZE_TIER_OPTIMISATION). This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_size_tier_suggestions(args: {
  // Optional Amazon ASIN, 10-char alphanumeric (e.g. B01LR5S6HK). Omit for all monitored products.
  asin?: string | null;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Marketplace code: US, UK, DE, JP, etc.
  marketplace: "US" | "CA" | "MX" | "DE" | "ES" | "IT" | "FR" | "UK" | "IN" | "NL" | "AU" | "JP" | "AE" | "BR" | "SA";
  // Helium 10 seller identifier.
  seller_id: string;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{
  // Size Tier optimization suggestions. Null on error.
  data: {
  // Optimization suggestions, one per monitored ASIN with a computed suggestion. Empty list when none apply.
  suggestions?: Array<{
  // Target ASIN.
  asin?: string | null;
  // Current FBA fulfillment fee.
  current_fba_fee?: number | null;
  // Current FBA Size Tier.
  current_size_tier?: number | null;
  // 0-4 entries, one per dimension (height/length/width/weight) that needs to change. Empty list when the Size Tier change needs no dimension adjustment.
  dimension_change_needed?: Array<{
  // Current value for this dimension.
  current_value?: number | null;
  // Which dimension changed: height, length, width, or weight.
  key?: string | null;
  // Optimization-suggested value for this dimension.
  optimization_value?: number | null;
}> | null;
  // Estimated FBA fulfillment fee after optimization.
  estimated_new_fee?: number | null;
  // Estimated savings (current_fba_fee - estimated_new_fee).
  estimated_savings?: number | null;
  // Suggested target FBA Size Tier.
  optimization_size_tier?: number | null;
  // Seller SKU, uniquely identifies the listing.
  seller_sku?: string | null;
}> | null;
} | null;
  error: null;
} | {
  data: null;
  // Structured error response from io-api.
  error: {
  // MCP string error code.
  code: string;
  // Optional structured error details.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Optional human-readable remediation guidance (may be omitted).
  remediation?: string;
};
}>>; };
```

## 78. `get_tiktok_product_influencers`

Helium 10 TikTok Product Influencers. Given a product_id from search_tiktok_products, returns the TikTok creators who have promoted or sold that specific product ("Collaborating Influencers"), with the GMV/units-sold/video count each creator generated for that product over a selected time window. Not a general influencer search — use search_tiktok_influencers for that. `limit` accepts 10-100 rows per call (default 10). At most 500 rows are reachable in total for one query; beyond that the result is truncated (truncation_reason='traversal_bound'). If data.pagination.has_more is true the result is incomplete: call again with `cursor` = data.pagination.next_cursor, but prefer narrowing filters over paginating through everything. Cursors expire 30 minutes after being issued and are bound to the query that issued them. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_tiktok_product_influencers(args: {
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Opaque pagination cursor from a previous response's pagination.next_cursor. Omit for the first page. Pass it back verbatim with the same query arguments (limit may change between pages); never construct or edit it. Cursors are reusable but expire 30 minutes after being issued (pagination.cursor_expires_at) and are bound to the query that issued them.
  cursor?: string | null;
  // Time window in days used to calculate the metrics.
  last_few_days?: 7 | 14 | 30;
  // Number of rows to return, 10-100 (default 10); out-of-range values are rejected. At most 500 rows are reachable in total for one query. When pagination.has_more is true, fetch the next batch with `cursor`. Do NOT probe with a small limit to learn the dataset size — every page carries the same total_count/has_more metadata, so ask for the rows you actually need and read the metadata of that response.
  limit?: number;
  // The TikTok Shop product to look up collaborating influencers for, taken from a search_tiktok_products result's id.
  product_id: string;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Sort ascending instead of the default descending order.
  sort_asc?: boolean;
  // Field to sort the results by.
  sort_by?: "gmv" | "itemsSold";
}): Promise<CallToolResult<{
  // Success payload; full schema in the top-level data property.
  data: { [key: string]: unknown; };
  // No error on success.
  error: null;
} | {
  // No data when the tool fails.
  data: null;
  // Error payload; full schema in the top-level error property.
  error: { [key: string]: unknown; };
}>>; };
```

## 79. `get_top_keywords`

Helium 10 Top Keywords for Listing. For an ASIN group, returns keyword data in two layers — an aggregate summary and a per-keyword detail table (search volume, keyword sales, CPR, organic rank, per-ASIN ranks) — plus a side-by-side view of which keywords competitors rank for that you don't. Summary is always returned. Field names differ by mode: single ASIN mode (no competitors) returns total_keywords, top10_keywords, total_search_volume, top10_search_volume; multi ASIN mode (with competitors) returns total_shared_keywords, shared_top10_keywords, total_shared_search_volume, shared_top10_search_volume. The response includes a mode field ("single" or "multi") so the caller knows which field set to expect. Keyword details are conditionally returned. In multi-ASIN mode, a keyword qualifies when: at least 2 ASINs share it, average competitor rank is between 1–40, at least 4 ranking competitors, and search volume > 500. In single-ASIN mode, a keyword qualifies when: word count ≥ 2, search volume > 500, and organic rank ≤ 10. When no keywords qualify, keyword_details returns an empty array [] and no_ranking_keywords is set to true. Use to see which keywords drive the most traffic, find coverage gaps versus competitors. Organic ranking signals only — paid keyword data is out of scope. Not for: general attribute comparison (use compare_listings); LQS or sales data (use get_listing_score). This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_top_keywords(args: {
  // Alias for ``main_asin`` (accepted because callers often send ``asin``). Prefer ``main_asin``; when both are supplied ``main_asin`` wins. Leave unset if you already pass ``main_asin``.
  asin?: string | null;
  // Optional list of up to 10 competitor ASINs (each 10 characters). When non-empty the response switches to multi mode: per-phrase rows gain shared-rank metrics and the aggregates move to summary_multi (Shared* variants). When omitted or empty the call runs in single mode.
  competitor_asins?: Array<string> | null;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Main Amazon ASIN whose keyword set anchors the response (10 characters). Required — the parameter is named ``main_asin`` (not ``asin``); you may also pass it via the ``asin`` alias.
  main_asin?: string | null;
  // Optional marketplace short code where the ASIN is listed. Supported: US, CA, MX, DE, ES, IT, FR, UK, IN, NL, AE, BR, AU. Defaults to US when omitted. Case-insensitive; whitespace is trimmed.
  marketplace?: string;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{
  // Keyword data for the session ASIN group, split into two layers (single vs multi). Aggregate field names differ by mode (see ``mode``); ``keyword_details`` is the per-phrase passthrough.
  data: {
  // Top keyword phrases for the session. Element shape differs by mode:
  // * single mode — phrase + organic_position + search_volume + monthly_sales (Keyword Sales) + new_cpr_exact / new_cpr_broad (CPR, rounded integers) + search_volume_trend30 + iq + cpc / high_cpc / low_cpc + amazon_choice + results_number + match-type fields.
  // * multi mode — phrase + organic_position + search_volume + monthly_sales (Keyword Sales) + competitive_avg (Organic Rank Avg) + ranking_competitors + position_range + new_cpr_exact / new_cpr_broad (CPR, rounded integers) + search_volume_trend30 + competitor_performance_score (Performance Score Avg) + amazon_choice + match_type.
  keyword_details?: Array<{
  // CPR (Broad) — same definition as ``new_cpr_exact`` but computed against the broad-title-match competitor pool. Also rounded to an integer.
  new_cpr_broad?: number | null;
  // CPR (Exact) — Estimated number of units that need to be sold over 8 days, resulting from a search for this keyword in order for the product to rank on top half page one. Rounded to an integer (the raw value is a float, e.g. 355.85).
  new_cpr_exact?: number | null;
}> | null;
  // ``single`` when the session has only the main ASIN; ``multi`` when competitor ASINs are present.
  mode?: string | null;
  // True when ``keyword_details`` is empty (no top-ranking keywords found).
  no_ranking_keywords?: boolean | null;
  // Multi-mode keyword aggregates. Populated when ``mode == 'multi'``; omitted (key absent) when ``mode == 'single'``.
  summary_multi?: {
  // Number of keywords that at least 2 ASINs organically rank on with average rank ≤ 10.
  shared_top10_keywords?: number | null;
  // Shared Top 10 Search Volume — Combined Search Volume generated by Shared Top 10 Keywords.
  shared_top10_search_volume?: number | null;
  // Total Shared Keywords — Number of keywords where at least 2 ASINs are organically in the top 306.
  total_shared_keywords?: number | null;
  // Total Shared Search Volume — Combined Search Volume generated by Total Shared Keywords.
  total_shared_search_volume?: number | null;
} | null;
  // Single-mode keyword aggregates. Populated when ``mode == 'single'``; omitted (key absent) when ``mode == 'multi'``.
  summary_single?: {
  // Number of keywords for which this ASIN organically ranks in the top 10 (rank ≤ 10).
  top10_keywords?: number | null;
  // Top 10 Search Volume — Combined Search Volume generated by Top 10 Keywords.
  top10_search_volume?: number | null;
  // Total Keywords — Number of keywords this ASIN organically ranks in the top 306.
  total_keywords?: number | null;
  // Total Search Volume — Combined Search Volume generated by Total Keywords.
  total_search_volume?: number | null;
} | null;
};
  // Always null — Listing Analyzer surfaces failures as an MCP error result (isError=true), not as an inline error payload.
  error: null;
}>>; };
```

## 80. `get_wmt_account_profit_and_loss_breakdown`

Return a WALMART account-level P&L snapshot with nested component metrics ONLY for the requested breakdown_metrics. Not a time series, not product rows. Walmart only. Use for drill-down: Walmart fee lines (walmart_fees → Commission), gross_revenue parts (Sales / Shipping / Taxes → Tax Collected + Tax Withheld), COGS parts (Product Cost / Shipping Cost / Tariffs), and named indirect costs (dynamic configured cost names). Pass only the needed keys from: cost_of_goods | indirect_cost | gross_revenue | walmart_fees — any other key is INVALID_INPUT. Prefer get_wmt_account_profit_and_loss_summary first for Level 1 totals. NOT for: Level 1 only (summary); component trends over time (get_wmt_account_profit_and_loss_breakdown_series); per-product drill-down (get_wmt_product_profit_and_loss_breakdown); Amazon P&L (unprefixed Amazon tools). seller_ids are Walmart partner_id values; marketplace / date_tz / currency are bound — today only US + America/Los_Angeles + USD. item_ids / skus only narrow the account roll-up. cost_of_goods / indirect_cost 0 may mean unconfigured. Do not mix with Amazon P&L. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_wmt_account_profit_and_loss_breakdown(args: {
  // Required, non-empty. Which Level 1 parents to expand into component trees; pass only the keys you need. Allowed (Epic HP-1781 §D): cost_of_goods (Product Cost / Shipping Cost / Tariffs), indirect_cost (dynamic configured cost names), gross_revenue (Sales / Shipping / Taxes → Tax Collected + Tax Withheld), walmart_fees (Commission). Any other key → INVALID_INPUT.
  breakdown_metrics: Array<"cost_of_goods" | "indirect_cost" | "gross_revenue" | "walmart_fees">;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // ISO 4217 code for amounts. Bound to the Walmart marketplace; today only USD (US). Optional, defaults to USD.
  currency?: "USD";
  // Inclusive start calendar date in date_tz, YYYY-MM-DD only (no time). Required. Use the exact parameter name current_date_from; do not use start_date, date_from, or from_date. Must be <= current_date_to.
  current_date_from: string;
  // Inclusive end calendar date in date_tz, YYYY-MM-DD only (no time). Required. Use the exact parameter name current_date_to; do not use end_date, date_to, or to_date. Must be >= current_date_from. A value after today in date_tz is silently clamped to today in date_tz (the future has no data).
  current_date_to: string;
  // Timezone used to interpret calendar dates. Bound to the Walmart marketplace (not any IANA timezone): today only America/Los_Angeles (US). Optional; defaults to America/Los_Angeles.
  date_tz?: "America/Los_Angeles";
  // Optional. Exact IN filter by Walmart item_id list (array of strings). Narrows the account roll-up only — the response stays a single metrics map, never product rows. Default null (all items).
  item_ids?: Array<string> | null;
  // Optional Walmart marketplace filter as an array of country short codes. Only US is enabled today (CA / MX reserved); default null = all authorised Walmart marketplaces.
  marketplace?: Array<string> | null;
  // Optional. Filter on Walmart seller accounts (array of partner_id strings). Intersected with the sellers authorised on the Pacvue token; no intersection → empty metrics object. Default null (all authorised Walmart sellers).
  seller_ids?: Array<string> | null;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Optional. Exact IN filter by seller SKU list (array of strings). Narrows the account roll-up only — the response stays a single metrics map, never product rows. Default null (all SKUs).
  skus?: Array<string> | null;
}): Promise<CallToolResult<{
  // Walmart account-level P&L breakdown snapshot for the requested breakdown_metrics only. Not a time series, no product rows.
  data: {
  // Only the requested breakdown_metrics keys appear (Epic HP-1781 §D allowlist: cost_of_goods, indirect_cost, gross_revenue, walmart_fees), each a recursive {name, value, metrics?} tree: cost_of_goods → Product Cost / Shipping Cost / Tariffs; indirect_cost → configured cost names (dynamic); gross_revenue → Sales / Shipping / Taxes (→ Tax Collected / Tax Withheld); walmart_fees → Commission. An empty object ({}) means no authorised Walmart sellers matched the request; a connected store with no transactions returns the requested parents with all-zero values. Expense trees are negative when non-zero; cost_of_goods / indirect_cost 0 may mean unconfigured.
  metrics: { [key: string]: {
  // Component children of this Level 1 parent (recursive tree). Omitted when the parent has no components (e.g. indirect_cost with no configured cost rules).
  metrics?: Array<{
  // Nested sub-components, recursive (e.g. Taxes → Tax Collected / Tax Withheld). Leaf nodes omit this field entirely — never null, never an empty array.
  metrics?: Array<{
  // Nested sub-components, recursive (e.g. Taxes → Tax Collected / Tax Withheld). Leaf nodes omit this field entirely — never null, never an empty array.
  metrics?: Array<unknown> | null;
  // Readable component label, e.g. 'Commission', 'Product Cost', 'Taxes', or a configured indirect-cost rule name. Never a snake_case key.
  name: string;
  // Component amount in the major currency unit, 2 dp. Expense-tree components (cost_of_goods / indirect_cost / walmart_fees children) are negative when non-zero; gross_revenue components keep their natural sign (Tax Withheld is negative).
  value: number;
}> | null;
  // Readable component label, e.g. 'Commission', 'Product Cost', 'Taxes', or a configured indirect-cost rule name. Never a snake_case key.
  name: string;
  // Component amount in the major currency unit, 2 dp. Expense-tree components (cost_of_goods / indirect_cost / walmart_fees children) are negative when non-zero; gross_revenue components keep their natural sign (Tax Withheld is negative).
  value: number;
}> | null;
  // Readable Level 1 label, e.g. 'Walmart Fees', 'Gross Revenue', 'COGS'.
  name: string;
  // Level 1 total in the major currency unit, 2 dp. Expense parents (cost_of_goods, indirect_cost, walmart_fees) are negative when non-zero.
  value: number;
}; };
} | null;
  // Null on success; error object on failure.
  error?: {
  // Error code string, e.g. 'INVALID_INPUT.DATE_RANGE_INVALID'.
  code: string;
  // Additional error context keyed by field or topic.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Suggested action to resolve the error.
  remediation: string;
} | null;
  // Optional non-blocking advisory (spec §6.1). Present only when the platform attaches one — currently a quota near-limit reminder (code QUOTA_NEAR_LIMIT). Null/absent on the vast majority of responses. Independent of 'error': the call still succeeded.
  notice?: {
  // Notice code, e.g. 'QUOTA_NEAR_LIMIT'.
  code: string;
  // Human-readable advisory message for the end user.
  message: string;
  // Advisory severity, e.g. 'warning'.
  severity: string;
} | null;
}>>; };
```

## 81. `get_wmt_account_profit_and_loss_breakdown_series`

Return WALMART account-level P&L breakdown component trees for the requested breakdown_metrics, bucketed by granularity (day / week / month) — every node carries a date-keyed values map alongside its range total. Not product-level. Walmart only. Use for trends of fee / revenue / COGS / indirect components over time (e.g. weekly Commission, monthly Tax Withheld, named indirect costs per month). Requires granularity AND a non-empty breakdown_metrics from: cost_of_goods | indirect_cost | gross_revenue | walmart_fees — any other key is INVALID_INPUT. Prefer simpler tools first: snapshot component totals → get_wmt_account_profit_and_loss_breakdown; Level 1 trends → get_wmt_account_profit_and_loss_summary_series; product trends → get_wmt_product_profit_and_loss_breakdown_series; Amazon P&L → unprefixed Amazon tools. Do not pass scope or offset — responses carry neither, use the request granularity you sent. seller_ids are Walmart partner_id values; marketplace / date_tz / currency are bound — today only US + America/Los_Angeles + USD. item_ids / skus only narrow the account roll-up. cost_of_goods / indirect_cost 0 may mean unconfigured. Do not mix with Amazon P&L. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_wmt_account_profit_and_loss_breakdown_series(args: {
  // Required, non-empty. Which Level 1 parents to expand into component trees; pass only the keys you need. Allowed (Epic HP-1781 §D): cost_of_goods (Product Cost / Shipping Cost / Tariffs), indirect_cost (dynamic configured cost names), gross_revenue (Sales / Shipping / Taxes → Tax Collected + Tax Withheld), walmart_fees (Commission). Any other key → INVALID_INPUT.
  breakdown_metrics: Array<"cost_of_goods" | "indirect_cost" | "gross_revenue" | "walmart_fees">;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // ISO 4217 code for amounts. Bound to the Walmart marketplace; today only USD (US). Optional, defaults to USD.
  currency?: "USD";
  // Inclusive start calendar date in date_tz, YYYY-MM-DD only (no time). Required. Use the exact parameter name current_date_from; do not use start_date, date_from, or from_date. Must be <= current_date_to.
  current_date_from: string;
  // Inclusive end calendar date in date_tz, YYYY-MM-DD only (no time). Required. Use the exact parameter name current_date_to; do not use end_date, date_to, or to_date. Must be >= current_date_from. A value after today in date_tz is silently clamped to today in date_tz (the future has no data).
  current_date_to: string;
  // Timezone used to interpret calendar dates. Bound to the Walmart marketplace (not any IANA timezone): today only America/Los_Angeles (US). Optional; defaults to America/Los_Angeles.
  date_tz?: "America/Los_Angeles";
  // Time-bucket size for series tools: day | week | month. Required. Use the exact parameter name granularity; do not use scope, interval, period, or bucket as input aliases. Week/month align to calendar boundaries (week starts Monday; month starts on the 1st); first/last buckets may be partial. The first bucket key may precede current_date_from (e.g. from 2026-07-01 at week granularity → first key 2026-06-29). Partial buckets include only data inside the requested date range. Series responses carry no scope or offset fields; use the request granularity you sent.
  granularity: "day" | "week" | "month";
  // Optional. Exact IN filter by Walmart item_id list (array of strings). Narrows the account roll-up only — the response stays a single metrics map, never product rows. Default null (all items).
  item_ids?: Array<string> | null;
  // Optional Walmart marketplace filter as an array of country short codes. Only US is enabled today (CA / MX reserved); default null = all authorised Walmart marketplaces.
  marketplace?: Array<string> | null;
  // Optional. Filter on Walmart seller accounts (array of partner_id strings). Intersected with the sellers authorised on the Pacvue token; no intersection → empty metrics object. Default null (all authorised Walmart sellers).
  seller_ids?: Array<string> | null;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Optional. Exact IN filter by seller SKU list (array of strings). Narrows the account roll-up only — the response stays a single metrics map, never product rows. Default null (all SKUs).
  skus?: Array<string> | null;
}): Promise<CallToolResult<{
  // Walmart account-level P&L breakdown component trees combined with time series, bucketed by the requested granularity. No product rows.
  data: {
  // Only the requested breakdown_metrics keys appear (Epic HP-1781 §D allowlist: cost_of_goods, indirect_cost, gross_revenue, walmart_fees), each the same recursive component tree as the snapshot breakdown with a date-keyed values map added to every node ({name, value, values, metrics?}). An empty object ({}) means no authorised Walmart sellers matched the request; a connected store with no transactions returns the requested parents as zero-filled trees. Expense trees are negative when non-zero; cost_of_goods / indirect_cost 0 may mean unconfigured.
  metrics: { [key: string]: {
  // Component children of this Level 1 parent, each with the same date-keyed values map (recursive tree). Omitted when the parent has no components (e.g. indirect_cost with no configured cost rules).
  metrics?: Array<{
  // Nested sub-components, recursive (e.g. Taxes → Tax Collected / Tax Withheld). Leaf nodes omit this field entirely — never null, never an empty array.
  metrics?: Array<{
  // Nested sub-components, recursive (e.g. Taxes → Tax Collected / Tax Withheld). Leaf nodes omit this field entirely — never null, never an empty array.
  metrics?: Array<unknown> | null;
  // Readable component label, e.g. 'Commission', 'Product Cost', 'Taxes', or a configured indirect-cost rule name. Never a snake_case key.
  name: string;
  // Requested-range total for this component (= sum of its values map), major currency unit, 2 dp. Expense-tree components are negative when non-zero; gross_revenue components keep natural signs.
  value: number;
  // Bucket-start date (yyyy-MM-dd) → component amount, zero-filled over the requested range. Same bucket axis as the parent (week starts Monday; month buckets on the 1st; the first key may precede current_date_from for partial natural-calendar buckets).
  values: { [key: string]: number; };
}> | null;
  // Readable component label, e.g. 'Commission', 'Product Cost', 'Taxes', or a configured indirect-cost rule name. Never a snake_case key.
  name: string;
  // Requested-range total for this component (= sum of its values map), major currency unit, 2 dp. Expense-tree components are negative when non-zero; gross_revenue components keep natural signs.
  value: number;
  // Bucket-start date (yyyy-MM-dd) → component amount, zero-filled over the requested range. Same bucket axis as the parent (week starts Monday; month buckets on the 1st; the first key may precede current_date_from for partial natural-calendar buckets).
  values: { [key: string]: number; };
}> | null;
  // Readable Level 1 label, e.g. 'Walmart Fees', 'Gross Revenue', 'COGS'.
  name: string;
  // Requested-range total (= sum of the values map), major currency unit, 2 dp. Expense parents (cost_of_goods, indirect_cost, walmart_fees) are negative when non-zero.
  value: number;
  // Bucket-start date (yyyy-MM-dd) → amount, zero-filled over the requested range; bucket size comes from the request granularity (never echoed as scope/offset).
  values: { [key: string]: number; };
}; };
} | null;
  // Null on success; error object on failure.
  error?: {
  // Error code string, e.g. 'INVALID_INPUT.DATE_RANGE_INVALID'.
  code: string;
  // Additional error context keyed by field or topic.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Suggested action to resolve the error.
  remediation: string;
} | null;
  // Optional non-blocking advisory (spec §6.1). Present only when the platform attaches one — currently a quota near-limit reminder (code QUOTA_NEAR_LIMIT). Null/absent on the vast majority of responses. Independent of 'error': the call still succeeded.
  notice?: {
  // Notice code, e.g. 'QUOTA_NEAR_LIMIT'.
  code: string;
  // Human-readable advisory message for the end user.
  message: string;
  // Advisory severity, e.g. 'warning'.
  severity: string;
} | null;
}>>; };
```

## 82. `get_wmt_account_profit_and_loss_summary`

Return WALMART account-level P&L summary for a time range: all Level 1 metrics as one snapshot map (sales, units_sold, refund, advertising_cost, cost_of_goods, indirect_cost, estimated_payout, gross_revenue, gross_profit, walmart_fees, expenses, net_profit, margin, roi, refunds_percentage). No child breakdowns, not a time series, not product rows. Walmart only. Use for overall / store-level Walmart Level 1 KPIs for a period without item/SKU rows or trend buckets; prefer this summary first. NOT for: Amazon P&L (use the unprefixed get_account_profit_and_loss_* / get_product_* tools); per-item or SKU figures (get_wmt_product_profit_and_loss_summary); trends (get_wmt_account_profit_and_loss_summary_series); line-item drill-down (get_wmt_account_profit_and_loss_breakdown); ACOS / TACoS / ads sales (Ads MCP — this tool only carries advertising_cost as negative spend); COGS / indirect-cost configuration CRUD. seller_ids are Walmart partner_id values; omit to include all token-authorised Walmart sellers. marketplace / date_tz / currency are bound together — today only US + America/Los_Angeles + USD. item_ids / skus only narrow the account roll-up (still a single metrics map). If cost_of_goods is 0 it may simply be unconfigured — say so, as net_profit / roi may be overstated. Do not invent tacos, ads_acos, or sessions for Walmart, and do not mix these numbers with Amazon P&L. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_wmt_account_profit_and_loss_summary(args: {
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // ISO 4217 code for amounts. Bound to the Walmart marketplace; today only USD (US). Optional, defaults to USD.
  currency?: "USD";
  // Inclusive start calendar date in date_tz, YYYY-MM-DD only (no time). Required. Use the exact parameter name current_date_from; do not use start_date, date_from, or from_date. Must be <= current_date_to.
  current_date_from: string;
  // Inclusive end calendar date in date_tz, YYYY-MM-DD only (no time). Required. Use the exact parameter name current_date_to; do not use end_date, date_to, or to_date. Must be >= current_date_from. A value after today in date_tz is silently clamped to today in date_tz (the future has no data).
  current_date_to: string;
  // Timezone used to interpret calendar dates. Bound to the Walmart marketplace (not any IANA timezone): today only America/Los_Angeles (US). Optional; defaults to America/Los_Angeles.
  date_tz?: "America/Los_Angeles";
  // Optional. Exact IN filter by Walmart item_id list (array of strings). Narrows the account roll-up only — the response stays a single metrics map, never product rows. Default null (all items).
  item_ids?: Array<string> | null;
  // Optional Walmart marketplace filter as an array of country short codes. Only US is enabled today (CA / MX reserved); default null = all authorised Walmart marketplaces.
  marketplace?: Array<string> | null;
  // Optional. Filter on Walmart seller accounts (array of partner_id strings). Intersected with the sellers authorised on the Pacvue token; no intersection → empty metrics object. Default null (all authorised Walmart sellers).
  seller_ids?: Array<string> | null;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Optional. Exact IN filter by seller SKU list (array of strings). Narrows the account roll-up only — the response stays a single metrics map, never product rows. Default null (all SKUs).
  skus?: Array<string> | null;
}): Promise<CallToolResult<{
  // Walmart account-level P&L Level 1 totals for the requested date range. No breakdown trees, no series buckets, no product rows.
  data: {
  // Flat map of metric_key → numeric value for the whole Walmart account roll-up over the requested date range. All 15 Walmart Level 1 keys (Epic HP-1781 §C): sales, units_sold, refund, advertising_cost, cost_of_goods, indirect_cost, estimated_payout, gross_revenue, gross_profit, walmart_fees, expenses, net_profit, margin, roi, refunds_percentage. Expense metrics (refund, advertising_cost, cost_of_goods, indirect_cost, walmart_fees, expenses) are NEGATIVE when non-zero; cost_of_goods / indirect_cost are 0 when unconfigured. Monetary values: float in the major currency unit, 2 dp. Counts (units_sold): integer. Rates (margin, roi, refunds_percentage) are PERCENTAGE POINTS, already scaled — 88.30 means 88.30%, do NOT multiply by 100 again; refunds_percentage >= 0. A rate is null (never 0) when its denominator is 0, e.g. margin with no gross_revenue: null means 'not computable', 0 would assert a real 0%. No Amazon-only keys (sessions, promo, reimbursements, tacos, ads_acos, amazon_fees). An empty object ({}) means no authorised Walmart sellers matched the request (empty seller intersection); a connected store with no transactions returns all 15 keys as 0.
  metrics: { [key: string]: number | number | null; };
} | null;
  // Null on success; error object on failure.
  error?: {
  // Error code string, e.g. 'INVALID_INPUT.DATE_RANGE_INVALID'.
  code: string;
  // Additional error context keyed by field or topic.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Suggested action to resolve the error.
  remediation: string;
} | null;
  // Optional non-blocking advisory (spec §6.1). Present only when the platform attaches one — currently a quota near-limit reminder (code QUOTA_NEAR_LIMIT). Null/absent on the vast majority of responses. Independent of 'error': the call still succeeded.
  notice?: {
  // Notice code, e.g. 'QUOTA_NEAR_LIMIT'.
  code: string;
  // Human-readable advisory message for the end user.
  message: string;
  // Advisory severity, e.g. 'warning'.
  severity: string;
} | null;
}>>; };
```

## 83. `get_wmt_account_profit_and_loss_summary_series`

Return WALMART account-level Level 1 P&L as a time series bucketed by granularity (day / week / month) — one (metric_key, values) series per Level 1 metric; no child breakdowns, not product rows. Walmart only. Use for trends / over-time comparison of store-level KPIs (daily sales, weekly net profit, monthly walmart_fees, …); prefer the non-series get_wmt_account_profit_and_loss_summary unless a trend is explicitly needed. NOT for: snapshot totals (get_wmt_account_profit_and_loss_summary); product trends (get_wmt_product_profit_and_loss_summary_series); breakdown component trends (get_wmt_account_profit_and_loss_breakdown_series); Amazon P&L (unprefixed Amazon tools); ACOS / ads sales (Ads MCP). Requires granularity; do not pass scope or offset — series carry neither, use the request granularity you sent. seller_ids are Walmart partner_id values; marketplace / date_tz / currency are bound together — today only US + America/Los_Angeles + USD. item_ids / skus only narrow the account roll-up. cost_of_goods / indirect_cost 0 may mean unconfigured. Do not invent tacos, ads_acos, or sessions for Walmart, and do not mix these numbers with Amazon P&L. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_wmt_account_profit_and_loss_summary_series(args: {
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // ISO 4217 code for amounts. Bound to the Walmart marketplace; today only USD (US). Optional, defaults to USD.
  currency?: "USD";
  // Inclusive start calendar date in date_tz, YYYY-MM-DD only (no time). Required. Use the exact parameter name current_date_from; do not use start_date, date_from, or from_date. Must be <= current_date_to.
  current_date_from: string;
  // Inclusive end calendar date in date_tz, YYYY-MM-DD only (no time). Required. Use the exact parameter name current_date_to; do not use end_date, date_to, or to_date. Must be >= current_date_from. A value after today in date_tz is silently clamped to today in date_tz (the future has no data).
  current_date_to: string;
  // Timezone used to interpret calendar dates. Bound to the Walmart marketplace (not any IANA timezone): today only America/Los_Angeles (US). Optional; defaults to America/Los_Angeles.
  date_tz?: "America/Los_Angeles";
  // Time-bucket size for series tools: day | week | month. Required. Use the exact parameter name granularity; do not use scope, interval, period, or bucket as input aliases. Week/month align to calendar boundaries (week starts Monday; month starts on the 1st); first/last buckets may be partial. The first bucket key may precede current_date_from (e.g. from 2026-07-01 at week granularity → first key 2026-06-29). Partial buckets include only data inside the requested date range. Series responses carry no scope or offset fields; use the request granularity you sent.
  granularity: "day" | "week" | "month";
  // Optional. Exact IN filter by Walmart item_id list (array of strings). Narrows the account roll-up only — the response stays a single metrics map, never product rows. Default null (all items).
  item_ids?: Array<string> | null;
  // Optional Walmart marketplace filter as an array of country short codes. Only US is enabled today (CA / MX reserved); default null = all authorised Walmart marketplaces.
  marketplace?: Array<string> | null;
  // Optional. Filter on Walmart seller accounts (array of partner_id strings). Intersected with the sellers authorised on the Pacvue token; no intersection → empty metrics object. Default null (all authorised Walmart sellers).
  seller_ids?: Array<string> | null;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Optional. Exact IN filter by seller SKU list (array of strings). Narrows the account roll-up only — the response stays a single metrics map, never product rows. Default null (all SKUs).
  skus?: Array<string> | null;
}): Promise<CallToolResult<{
  // Walmart account-level P&L Level 1 metrics as time series bucketed by the requested granularity. No breakdown trees, no product rows.
  data: {
  // One {metric_key, values} entry per Level 1 key (all 15, Epic HP-1781 §C). All 15 Walmart Level 1 keys (Epic HP-1781 §C): sales, units_sold, refund, advertising_cost, cost_of_goods, indirect_cost, estimated_payout, gross_revenue, gross_profit, walmart_fees, expenses, net_profit, margin, roi, refunds_percentage. Expense metrics (refund, advertising_cost, cost_of_goods, indirect_cost, walmart_fees, expenses) are NEGATIVE when non-zero; cost_of_goods / indirect_cost are 0 when unconfigured. Monetary values: float in the major currency unit, 2 dp. Counts (units_sold): integer. Rates (margin, roi, refunds_percentage) are PERCENTAGE POINTS, already scaled — 88.30 means 88.30%, do NOT multiply by 100 again; refunds_percentage >= 0. A rate is null (never 0) when its denominator is 0, e.g. margin with no gross_revenue: null means 'not computable', 0 would assert a real 0%. No Amazon-only keys (sessions, promo, reimbursements, tacos, ads_acos, amazon_fees). An empty array ([]) means no authorised Walmart sellers matched the request; a connected store with no transactions returns 15 zero-filled series.
  metrics: Array<{
  // Level 1 metric key this series belongs to (Epic HP-1781 §C), e.g. 'walmart_fees'.
  metric_key: string;
  // Bucket-start date (yyyy-MM-dd) → numeric value, zero-filled over the requested range (missing buckets = 0). Bucket size comes from the request granularity (never echoed as scope/offset). Week buckets start Monday; month buckets on the 1st — the first key may precede current_date_from when a partial natural-calendar bucket overlaps the request, and partial buckets contain only requested-range data. Expense metrics are negative per bucket when non-zero; counts are integers; rates are percentage points.
  values: { [key: string]: number | number | null; };
}>;
} | null;
  // Null on success; error object on failure.
  error?: {
  // Error code string, e.g. 'INVALID_INPUT.DATE_RANGE_INVALID'.
  code: string;
  // Additional error context keyed by field or topic.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Suggested action to resolve the error.
  remediation: string;
} | null;
  // Optional non-blocking advisory (spec §6.1). Present only when the platform attaches one — currently a quota near-limit reminder (code QUOTA_NEAR_LIMIT). Null/absent on the vast majority of responses. Independent of 'error': the call still succeeded.
  notice?: {
  // Notice code, e.g. 'QUOTA_NEAR_LIMIT'.
  code: string;
  // Human-readable advisory message for the end user.
  message: string;
  // Advisory severity, e.g. 'warning'.
  severity: string;
} | null;
}>>; };
```

## 84. `get_wmt_inventory_values`

Return CURRENT WALMART inventory quantity metrics per product row. Each row includes product identity (item_id, sku, product_name, brand, image_url, seller_id, marketplace, fulfillment_type) plus FLAT WFS and SF quantity fields on the same row: wfs_available, wfs_reserved, wfs_on_hand, wfs_inbound, wfs_unavailable, sf_available, sf_on_hand. A Walmart SKU can hold BOTH WFS and SF stock at once (unlike Amazon FBA/FBM, which are separate SKUs/rows). Not a time series; NOT days of supply or inventory age — Walmart has no equivalent health API, so there is no Walmart counterpart to get_fba_inventory_health_status.

When to use: user asks how much Walmart stock is sellable, reserved, on hand, inbound to WFS, or unavailable; needs SKU / item_id-level inventory counts across sellers; restock or fulfillment planning from quantity snapshots; wants to filter or sort by any of the seven quantity fields (e.g. low wfs_available, high wfs_inbound).

When NOT to use: Amazon inventory quantities (get_inventory_values); Amazon FBA days of supply / inventory age (get_fba_inventory_health_status — no Walmart counterpart); Walmart units-sold trends over time (get_wmt_sales_velocity); Walmart profit & loss or fees (the Walmart P&L tools); discovering which Walmart sellers are linked (get_my_wmt_seller_accounts).

Guidance: prefer the narrowest filters (seller_ids, marketplace, item_ids, skus, fulfillment_type) plus *_min / *_max bounds and sort_by to keep responses small, and page through large catalogs. fulfillment_type selects products whose LISTING ATTRIBUTE matches — matching rows may still show non-zero quantities on the other channel (e.g. fulfillment_type=['WFS'] can return rows with sf_available > 0). Do not invent seller_id / item_id / sku values — call get_my_wmt_seller_accounts first for valid seller ids. Do not mix these numbers with Amazon inventory tools. Rows carry item_id, never a backend asin. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_wmt_inventory_values(args: { context?: string; fulfillment_type?: Array<string> | null; item_ids?: Array<string> | null; marketplace?: Array<string> | null; page_index?: number | null; page_size?: number | null; seller_ids?: Array<string> | null; session_id?: string; sf_available_max?: number | null; sf_available_min?: number | null; sf_on_hand_max?: number | null; sf_on_hand_min?: number | null; skus?: Array<string> | null; sort_by?: "wfs_available" | "wfs_reserved" | "wfs_on_hand" | "wfs_inbound" | "wfs_unavailable" | "sf_available" | "sf_on_hand"; sort_order?: "asc" | "desc"; wfs_available_max?: number | null; wfs_available_min?: number | null; wfs_inbound_max?: number | null; wfs_inbound_min?: number | null; wfs_on_hand_max?: number | null; wfs_on_hand_min?: number | null; wfs_reserved_max?: number | null; wfs_reserved_min?: number | null; wfs_unavailable_max?: number | null; wfs_unavailable_min?: number | null; }): Promise<CallToolResult<{
  // Current Walmart inventory quantity snapshot per product row. Not a time series; no days-of-supply or inventory age (Walmart has no equivalent health API).
  data: {
  // 1-based page number echoed back.
  page_index?: number | null;
  // Rows per page echoed back.
  page_size?: number | null;
  // One row per product (SKU × marketplace × seller) with both WFS and SF quantities flat on the row. Empty array when no product matches or no authorised Walmart sellers match. No account-level totals aggregate is returned.
  rows: Array<{
  // Product brand; may be null.
  brand?: string | null;
  // The product's listing attribute: 'WFS' (Walmart Fulfillment Services), 'WFSE' (WFS Eligible), or 'SF' (Seller Fulfilled). This is the listing attribute only — the row's quantities can still be non-zero on the other channel.
  fulfillment_type?: string | null;
  // Product image URL, when known.
  image_url?: string | null;
  // Walmart item_id of this product (never a backend asin).
  item_id?: string | null;
  // Marketplace country short code; today always 'US'.
  marketplace?: string | null;
  // Product listing title.
  product_name?: string | null;
  // Walmart seller (partner_id) that owns this listing.
  seller_id?: string | null;
  // Seller-Fulfilled units sellable right now. Always present (0 when none).
  sf_available: number;
  // Seller-Fulfilled units on hand. Always present (0 when none). There are no SF reserved / inbound / unavailable counts.
  sf_on_hand: number;
  // Seller SKU.
  sku?: string | null;
  // WFS units sellable right now. Always present (0 when none).
  wfs_available: number;
  // Units in transit to WFS. Always present (0 when none).
  wfs_inbound: number;
  // All units in WFS fulfillment centres (sellable + reserved). Always present (0 when none).
  wfs_on_hand: number;
  // WFS units held by orders. Always present (0 when none).
  wfs_reserved: number;
  // WFS units damaged or otherwise unsellable. Always present (0 when none).
  wfs_unavailable: number;
}>;
  // Matching rows after filters, before pagination (0 when empty).
  total_count?: number | null;
} | null;
  // Null on success; error object on failure.
  error?: {
  // Error code string, e.g. 'INVALID_INPUT.DATE_RANGE_INVALID'.
  code: string;
  // Additional error context keyed by field or topic.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Suggested action to resolve the error.
  remediation: string;
} | null;
  // Optional non-blocking advisory (spec §6.1). Present only when the platform attaches one — currently a quota near-limit reminder (code QUOTA_NEAR_LIMIT). Null/absent on the vast majority of responses. Independent of 'error': the call still succeeded.
  notice?: {
  // Notice code, e.g. 'QUOTA_NEAR_LIMIT'.
  code: string;
  // Human-readable advisory message for the end user.
  message: string;
  // Advisory severity, e.g. 'warning'.
  severity: string;
} | null;
}>>; };
```

## 85. `get_wmt_product_profit_and_loss_breakdown`

Return WALMART product-level P&L snapshot rows with nested component metrics ONLY for the requested breakdown_metrics: each row carries full product identity (item_id, sku, product_name, brand, image_url, seller_id, marketplace, fulfillment_type) plus a metrics map of recursive {name, value, metrics?} trees. Not a time series. Walmart only. Use for per-SKU / per-item_id drill-down: fee lines (walmart_fees → Commission), gross_revenue parts (Sales / Shipping / Taxes), COGS parts (Product Cost / Shipping Cost / Tariffs), named indirect costs (sku-level amortisations only — account/seller-level indirect costs cannot be attributed to a product). Requires non-empty breakdown_metrics from: cost_of_goods | indirect_cost | gross_revenue | walmart_fees — any other key is INVALID_INPUT. Prefer get_wmt_product_profit_and_loss_summary first; limit metrics and page_size. NOT for: product Level 1 only (product summary); account-level drill-down (get_wmt_account_profit_and_loss_breakdown); component trends over time (get_wmt_product_profit_and_loss_breakdown_series); Amazon P&L (unprefixed Amazon tools). seller_ids are Walmart partner_id values; marketplace / date_tz / currency are bound — today only US + America/Los_Angeles + USD. Output rows carry item_id, never a backend asin. cost_of_goods / indirect_cost 0 may mean unconfigured. Do not mix with Amazon P&L. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_wmt_product_profit_and_loss_breakdown(args: { breakdown_metrics: Array<"cost_of_goods" | "indirect_cost" | "gross_revenue" | "walmart_fees">; context?: string; currency?: "USD"; current_date_from: string; current_date_to: string; date_tz?: "America/Los_Angeles"; item_ids?: Array<string> | null; marketplace?: Array<string> | null; page_index?: number | null; page_size?: number | null; product_level?: "sku" | "item_id"; seller_ids?: Array<string> | null; session_id?: string; skus?: Array<string> | null; sort_by?: "sales" | "units_sold" | "refund" | "advertising_cost" | "cost_of_goods" | "indirect_cost" | "estimated_payout" | "gross_revenue" | "gross_profit" | "walmart_fees" | "expenses" | "net_profit" | "margin" | "roi" | "refunds_percentage"; sort_order?: "asc" | "desc"; }): Promise<CallToolResult<{
  // Walmart product-level P&L breakdown snapshots (paged rows) for the requested breakdown_metrics only. Not a time series.
  data: {
  // 1-based page number echoed back.
  page_index?: number | null;
  // Rows per page echoed back.
  page_size?: number | null;
  // One row per product at the requested product_level (sku default | item_id), sorted by sort_by/sort_order when provided. Empty array when no product matches or no authorised Walmart sellers match.
  rows: Array<{
  // Product brand, when known.
  brand?: string | null;
  // The product's listing attribute: 'WFS' (Walmart Fulfillment Services), 'WFSE' (WFS Eligible), or 'SF' (Seller Fulfilled) — the same short codes get_wmt_inventory_values returns and the fulfillment_type filter accepts, so a value read here can be passed straight back in (HP-2020).
  fulfillment_type?: string | null;
  // Product image URL, when known.
  image_url?: string | null;
  // Walmart item_id of this row's product (never a backend asin).
  item_id?: string | null;
  // Marketplace country short code; today always 'US'.
  marketplace?: string | null;
  // Only the requested breakdown_metrics keys appear (Epic HP-1781 §D allowlist), each a recursive {name, value, metrics?} tree for THIS product — same shape and signs as the account breakdown. indirect_cost children come from sku-level amortisations only (account/seller-level amortisations cannot be attributed to a product, so per-product indirect children may sum to less than the account-level total).
  metrics: { [key: string]: {
  // Component children of this Level 1 parent (recursive tree). Omitted when the parent has no components (e.g. indirect_cost with no configured cost rules).
  metrics?: Array<{
  // Nested sub-components, recursive (e.g. Taxes → Tax Collected / Tax Withheld). Leaf nodes omit this field entirely — never null, never an empty array.
  metrics?: Array<{
  // Nested sub-components, recursive (e.g. Taxes → Tax Collected / Tax Withheld). Leaf nodes omit this field entirely — never null, never an empty array.
  metrics?: Array<unknown> | null;
  // Readable component label, e.g. 'Commission', 'Product Cost', 'Taxes', or a configured indirect-cost rule name. Never a snake_case key.
  name: string;
  // Component amount in the major currency unit, 2 dp. Expense-tree components (cost_of_goods / indirect_cost / walmart_fees children) are negative when non-zero; gross_revenue components keep their natural sign (Tax Withheld is negative).
  value: number;
}> | null;
  // Readable component label, e.g. 'Commission', 'Product Cost', 'Taxes', or a configured indirect-cost rule name. Never a snake_case key.
  name: string;
  // Component amount in the major currency unit, 2 dp. Expense-tree components (cost_of_goods / indirect_cost / walmart_fees children) are negative when non-zero; gross_revenue components keep their natural sign (Tax Withheld is negative).
  value: number;
}> | null;
  // Readable Level 1 label, e.g. 'Walmart Fees', 'Gross Revenue', 'COGS'.
  name: string;
  // Level 1 total in the major currency unit, 2 dp. Expense parents (cost_of_goods, indirect_cost, walmart_fees) are negative when non-zero.
  value: number;
}; };
  // Product listing title.
  product_name?: string | null;
  // Walmart seller (partner_id) that owns this row.
  seller_id?: string | null;
  // Seller SKU of this row's product.
  sku?: string | null;
}>;
  // Total matching products across all pages (0 when empty).
  total_count?: number | null;
} | null;
  // Null on success; error object on failure.
  error?: {
  // Error code string, e.g. 'INVALID_INPUT.DATE_RANGE_INVALID'.
  code: string;
  // Additional error context keyed by field or topic.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Suggested action to resolve the error.
  remediation: string;
} | null;
  // Optional non-blocking advisory (spec §6.1). Present only when the platform attaches one — currently a quota near-limit reminder (code QUOTA_NEAR_LIMIT). Null/absent on the vast majority of responses. Independent of 'error': the call still succeeded.
  notice?: {
  // Notice code, e.g. 'QUOTA_NEAR_LIMIT'.
  code: string;
  // Human-readable advisory message for the end user.
  message: string;
  // Advisory severity, e.g. 'warning'.
  severity: string;
} | null;
}>>; };
```

## 86. `get_wmt_product_profit_and_loss_breakdown_series`

Return WALMART product-level P&L breakdown component trees over time: each row carries full product identity (item_id, sku, product_name, brand, image_url, seller_id, marketplace, fulfillment_type) plus a metrics map for the requested breakdown_metrics where every node has a date-keyed values map ({name, value, values, metrics?}), bucketed by granularity (day / week / month). This is the HIGHEST TOKEN COST tool in the Walmart P&L set — request minimal breakdown_metrics, narrow with item_ids/skus, and use a small page_size. Walmart only. Use for per-product trends of breakdown components (e.g. weekly Commission by SKU, monthly named indirect costs per item). Requires granularity AND non-empty breakdown_metrics from: cost_of_goods | indirect_cost | gross_revenue | walmart_fees — any other key is INVALID_INPUT. NOT for: snapshot component totals (get_wmt_product_profit_and_loss_breakdown); Level 1 product trends (get_wmt_product_profit_and_loss_summary_series); account-level component trends (get_wmt_account_profit_and_loss_breakdown_series); Amazon P&L (unprefixed Amazon tools). Do not pass scope or offset — responses carry neither, use the request granularity you sent. seller_ids are Walmart partner_id values; marketplace / date_tz / currency are bound — today only US + America/Los_Angeles + USD. Output rows carry item_id, never a backend asin; indirect_cost children are sku-level amortisations only. cost_of_goods / indirect_cost 0 may mean unconfigured. Do not mix with Amazon P&L. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_wmt_product_profit_and_loss_breakdown_series(args: { breakdown_metrics: Array<"cost_of_goods" | "indirect_cost" | "gross_revenue" | "walmart_fees">; context?: string; currency?: "USD"; current_date_from: string; current_date_to: string; date_tz?: "America/Los_Angeles"; granularity: "day" | "week" | "month"; item_ids?: Array<string> | null; marketplace?: Array<string> | null; page_index?: number | null; page_size?: number | null; product_level?: "sku" | "item_id"; seller_ids?: Array<string> | null; session_id?: string; skus?: Array<string> | null; sort_by?: "sales" | "units_sold" | "refund" | "advertising_cost" | "cost_of_goods" | "indirect_cost" | "estimated_payout" | "gross_revenue" | "gross_profit" | "walmart_fees" | "expenses" | "net_profit" | "margin" | "roi" | "refunds_percentage"; sort_order?: "asc" | "desc"; }): Promise<CallToolResult>; };
```

## 87. `get_wmt_product_profit_and_loss_summary`

Return WALMART product-level P&L snapshots: one row per product at product_level (sku default | item_id) with product identity (item_id, sku, product_name, brand, image_url, seller_id, marketplace, fulfillment_type) and ALL Level 1 metrics per row. No time buckets, no breakdown trees. Walmart only. Use to rank / compare / inspect per-SKU or per-item_id KPIs (e.g. top products by net_profit, SKUs with negative margin). Prefer account-level tools unless product granularity is explicitly needed. Use paging (page_index/page_size) plus sort_by (any Level 1 key) / sort_order (default desc) instead of fetching everything. NOT for: store roll-up (get_wmt_account_profit_and_loss_summary); product trends (get_wmt_product_profit_and_loss_summary_series); per-product fee drill-down (get_wmt_product_profit_and_loss_breakdown); Amazon product P&L (unprefixed Amazon tools). seller_ids are Walmart partner_id values; marketplace / date_tz / currency are bound — today only US + America/Los_Angeles + USD. Output rows carry item_id, never a backend asin. cost_of_goods / indirect_cost 0 may mean unconfigured — flag that profit may be overstated. Do not mix with Amazon P&L. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_wmt_product_profit_and_loss_summary(args: {
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // ISO 4217 code for amounts. Bound to the Walmart marketplace; today only USD (US). Optional, defaults to USD.
  currency?: "USD";
  // Inclusive start calendar date in date_tz, YYYY-MM-DD only (no time). Required. Use the exact parameter name current_date_from; do not use start_date, date_from, or from_date. Must be <= current_date_to.
  current_date_from: string;
  // Inclusive end calendar date in date_tz, YYYY-MM-DD only (no time). Required. Use the exact parameter name current_date_to; do not use end_date, date_to, or to_date. Must be >= current_date_from. A value after today in date_tz is silently clamped to today in date_tz (the future has no data).
  current_date_to: string;
  // Timezone used to interpret calendar dates. Bound to the Walmart marketplace (not any IANA timezone): today only America/Los_Angeles (US). Optional; defaults to America/Los_Angeles.
  date_tz?: "America/Los_Angeles";
  // Optional. Exact IN filter by Walmart item_id list (array of strings). Restricts which product rows are returned; the response is still one row per product. Default null (all items).
  item_ids?: Array<string> | null;
  // Optional Walmart marketplace filter as an array of country short codes. Only US is enabled today (CA / MX reserved); default null = all authorised Walmart marketplaces.
  marketplace?: Array<string> | null;
  // Optional. 1-based page number. Default 1.
  page_index?: number | null;
  // Optional. Rows per page, 1..100. Default 50.
  page_size?: number | null;
  // Row aggregation for product tools: sku (default) | item_id. One item_id may map to multiple SKUs. Walmart has no asin/parent_asin levels.
  product_level?: "sku" | "item_id";
  // Optional. Filter on Walmart seller accounts (array of partner_id strings). Intersected with the sellers authorised on the Pacvue token; no intersection → empty metrics object. Default null (all authorised Walmart sellers).
  seller_ids?: Array<string> | null;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Optional. Exact IN filter by seller SKU list (array of strings). Restricts which product rows are returned; the response is still one row per product. Default null (all SKUs).
  skus?: Array<string> | null;
  // Optional. Sort rows by any Walmart Level 1 metric key (Epic HP-1781 §C). Cost-type metrics (refund, advertising_cost, cost_of_goods, indirect_cost, walmart_fees, expenses) are returned as negative values and are ordered by ABSOLUTE amount, not signed value: sort_order=desc lists the highest spend/cost first and asc the lowest — same rule as the Amazon P&L tools. net_profit, margin and roi can also be negative but are NOT cost metrics: they keep signed ordering, so desc lists the most profitable first and losses last. Omit = backend default order.
  sort_by?: "sales" | "units_sold" | "refund" | "advertising_cost" | "cost_of_goods" | "indirect_cost" | "estimated_payout" | "gross_revenue" | "gross_profit" | "walmart_fees" | "expenses" | "net_profit" | "margin" | "roi" | "refunds_percentage";
  // Optional. Sort direction for sort_by. 'desc' = highest value first (default); 'asc' = lowest value first. Ignored if sort_by is not provided.
  sort_order?: "asc" | "desc";
}): Promise<CallToolResult<{
  // Walmart product-level P&L Level 1 snapshots (paged rows). No time buckets, no breakdown trees.
  data: {
  // 1-based page number echoed back.
  page_index?: number | null;
  // Rows per page echoed back.
  page_size?: number | null;
  // One row per product at the requested product_level (sku default | item_id), sorted by sort_by/sort_order when provided. Empty array when no product matches or no authorised Walmart sellers match.
  rows: Array<{
  // Product brand, when known.
  brand?: string | null;
  // The product's listing attribute: 'WFS' (Walmart Fulfillment Services), 'WFSE' (WFS Eligible), or 'SF' (Seller Fulfilled) — the same short codes get_wmt_inventory_values returns and the fulfillment_type filter accepts, so a value read here can be passed straight back in (HP-2020).
  fulfillment_type?: string | null;
  // Product image URL, when known.
  image_url?: string | null;
  // Walmart item_id of this row's product (never a backend asin).
  item_id?: string | null;
  // Marketplace country short code; today always 'US'.
  marketplace?: string | null;
  // Flat map of metric_key → numeric value for this product over the requested date range. All 15 Walmart Level 1 keys (Epic HP-1781 §C): sales, units_sold, refund, advertising_cost, cost_of_goods, indirect_cost, estimated_payout, gross_revenue, gross_profit, walmart_fees, expenses, net_profit, margin, roi, refunds_percentage. Expense metrics (refund, advertising_cost, cost_of_goods, indirect_cost, walmart_fees, expenses) are NEGATIVE when non-zero; cost_of_goods / indirect_cost are 0 when unconfigured. Monetary values: float in the major currency unit, 2 dp. Counts (units_sold): integer. Rates (margin, roi, refunds_percentage) are PERCENTAGE POINTS, already scaled — 88.30 means 88.30%, do NOT multiply by 100 again; refunds_percentage >= 0. A rate is null (never 0) when its denominator is 0, e.g. margin with no gross_revenue: null means 'not computable', 0 would assert a real 0%. No Amazon-only keys (sessions, promo, reimbursements, tacos, ads_acos, amazon_fees).
  metrics: { [key: string]: number | number | null; };
  // Product listing title.
  product_name?: string | null;
  // Walmart seller (partner_id) that owns this row.
  seller_id?: string | null;
  // Seller SKU of this row's product.
  sku?: string | null;
}>;
  // Total matching products across all pages (0 when empty).
  total_count?: number | null;
} | null;
  // Null on success; error object on failure.
  error?: {
  // Error code string, e.g. 'INVALID_INPUT.DATE_RANGE_INVALID'.
  code: string;
  // Additional error context keyed by field or topic.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Suggested action to resolve the error.
  remediation: string;
} | null;
  // Optional non-blocking advisory (spec §6.1). Present only when the platform attaches one — currently a quota near-limit reminder (code QUOTA_NEAR_LIMIT). Null/absent on the vast majority of responses. Independent of 'error': the call still succeeded.
  notice?: {
  // Notice code, e.g. 'QUOTA_NEAR_LIMIT'.
  code: string;
  // Human-readable advisory message for the end user.
  message: string;
  // Advisory severity, e.g. 'warning'.
  severity: string;
} | null;
}>>; };
```

## 88. `get_wmt_product_profit_and_loss_summary_series`

Return WALMART product-level Level 1 P&L as time series per product row, bucketed by granularity (day / week / month): each row carries full product identity (item_id, sku, product_name, brand, image_url, seller_id, marketplace, fulfillment_type) plus metric_series[] with one (metric_key, values) entry per Level 1 metric. No breakdown trees. Walmart only. Use for per-SKU / per-item_id trends (weekly sales by SKU, monthly margin by item_id). Requires granularity. Narrow with item_ids/skus and a small page_size — the payload grows with products × buckets × 15 metrics. Paging/sort follow the product summary tool (sort_by = any Level 1 key, sort_order default desc, sorted on the range total). NOT for: product snapshot totals (get_wmt_product_profit_and_loss_summary); account-level trends (get_wmt_account_profit_and_loss_summary_series); product fee-component trends (get_wmt_product_profit_and_loss_breakdown_series); Amazon P&L (unprefixed Amazon tools). Do not pass scope or offset — responses carry neither, use the request granularity you sent. seller_ids are Walmart partner_id values; marketplace / date_tz / currency are bound — today only US + America/Los_Angeles + USD. Output rows carry item_id, never a backend asin. cost_of_goods / indirect_cost 0 may mean unconfigured. Do not mix with Amazon P&L. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_wmt_product_profit_and_loss_summary_series(args: { context?: string; currency?: "USD"; current_date_from: string; current_date_to: string; date_tz?: "America/Los_Angeles"; granularity: "day" | "week" | "month"; item_ids?: Array<string> | null; marketplace?: Array<string> | null; page_index?: number | null; page_size?: number | null; product_level?: "sku" | "item_id"; seller_ids?: Array<string> | null; session_id?: string; skus?: Array<string> | null; sort_by?: "sales" | "units_sold" | "refund" | "advertising_cost" | "cost_of_goods" | "indirect_cost" | "estimated_payout" | "gross_revenue" | "gross_profit" | "walmart_fees" | "expenses" | "net_profit" | "margin" | "roi" | "refunds_percentage"; sort_order?: "asc" | "desc"; }): Promise<CallToolResult<{
  // Walmart product-level P&L Level 1 time series (paged rows, bucketed by the requested granularity). No breakdown trees.
  data: {
  // 1-based page number echoed back.
  page_index?: number | null;
  // Rows per page echoed back.
  page_size?: number | null;
  // One row per product at the requested product_level (sku default | item_id), each with full product identity plus metric_series[]. Empty array when no product matches or no authorised Walmart sellers match. Payload grows with products × buckets × 15 metrics — narrow with item_ids/skus and small page_size.
  rows: Array<{
  // Product brand, when known.
  brand?: string | null;
  // The product's listing attribute: 'WFS' (Walmart Fulfillment Services), 'WFSE' (WFS Eligible), or 'SF' (Seller Fulfilled) — the same short codes get_wmt_inventory_values returns and the fulfillment_type filter accepts, so a value read here can be passed straight back in (HP-2020).
  fulfillment_type?: string | null;
  // Product image URL, when known.
  image_url?: string | null;
  // Walmart item_id of this row's product (never a backend asin).
  item_id?: string | null;
  // Marketplace country short code; today always 'US'.
  marketplace?: string | null;
  // One {metric_key, values} entry per Level 1 key (all 15, Epic HP-1781 §C) for THIS product, same shape as the account summary series. All 15 Walmart Level 1 keys (Epic HP-1781 §C): sales, units_sold, refund, advertising_cost, cost_of_goods, indirect_cost, estimated_payout, gross_revenue, gross_profit, walmart_fees, expenses, net_profit, margin, roi, refunds_percentage. Expense metrics (refund, advertising_cost, cost_of_goods, indirect_cost, walmart_fees, expenses) are NEGATIVE when non-zero; cost_of_goods / indirect_cost are 0 when unconfigured. Monetary values: float in the major currency unit, 2 dp. Counts (units_sold): integer. Rates (margin, roi, refunds_percentage) are PERCENTAGE POINTS, already scaled — 88.30 means 88.30%, do NOT multiply by 100 again; refunds_percentage >= 0. A rate is null (never 0) when its denominator is 0, e.g. margin with no gross_revenue: null means 'not computable', 0 would assert a real 0%. No Amazon-only keys (sessions, promo, reimbursements, tacos, ads_acos, amazon_fees).
  metric_series: Array<{
  // Level 1 metric key this series belongs to (Epic HP-1781 §C), e.g. 'walmart_fees'.
  metric_key: string;
  // Bucket-start date (yyyy-MM-dd) → numeric value, zero-filled over the requested range (missing buckets = 0). Bucket size comes from the request granularity (never echoed as scope/offset). Week buckets start Monday; month buckets on the 1st — the first key may precede current_date_from when a partial natural-calendar bucket overlaps the request, and partial buckets contain only requested-range data. Expense metrics are negative per bucket when non-zero; counts are integers; rates are percentage points.
  values: { [key: string]: number | number | null; };
}>;
  // Product listing title.
  product_name?: string | null;
  // Walmart seller (partner_id) that owns this row.
  seller_id?: string | null;
  // Seller SKU of this row's product.
  sku?: string | null;
}>;
  // Total matching products across all pages (0 when empty).
  total_count?: number | null;
} | null;
  // Null on success; error object on failure.
  error?: {
  // Error code string, e.g. 'INVALID_INPUT.DATE_RANGE_INVALID'.
  code: string;
  // Additional error context keyed by field or topic.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Suggested action to resolve the error.
  remediation: string;
} | null;
  // Optional non-blocking advisory (spec §6.1). Present only when the platform attaches one — currently a quota near-limit reminder (code QUOTA_NEAR_LIMIT). Null/absent on the vast majority of responses. Independent of 'error': the call still succeeded.
  notice?: {
  // Notice code, e.g. 'QUOTA_NEAR_LIMIT'.
  code: string;
  // Human-readable advisory message for the end user.
  message: string;
  // Advisory severity, e.g. 'warning'.
  severity: string;
} | null;
}>>; };
```

## 89. `get_wmt_sales_velocity`

Return WALMART units sold over time (sales velocity) per SKU row, bucketed by day / week / month over an inclusive date range. Each row carries product identity (item_id, sku, product_name, brand, image_url, seller_id, marketplace, fulfillment_type) plus a single sales_velocity.values map (bucket-start yyyy-MM-dd → units sold). Sales velocity is INDEPENDENT of inventory channels: one SKU = one sales series (e.g. SKU A sold 100 units), regardless of how that SKU's stock splits across WFS and SF — do not reconcile these unit series against the WFS/SF inventory fields, they are different domains. Not a quantity snapshot; not revenue / fees / other P&L metrics; not days of supply or inventory age (Walmart has no health tool).

When to use: user asks how Walmart units sold TREND, wants to compare periods, needs velocity by SKU / item_id, or needs time-bucketed unit counts for restock planning alongside get_wmt_inventory_values.

When NOT to use: Walmart inventory quantity snapshot (get_wmt_inventory_values); Amazon sales velocity (get_sales_velocity); full Walmart P&L trends — sales $, fees, profit — (get_wmt_product_profit_and_loss_summary_series or the account series tools); discovering linked Walmart sellers (get_my_wmt_seller_accounts).

Guidance: prefer this tool over the Walmart product P&L series when ONLY units sold matter — the payload is much smaller. Empty {} is valid: it defaults to the last 30 inclusive calendar days in date_tz with granularity=day. Provide BOTH current_date_from and current_date_to or NEITHER — supplying only one is an error. Narrow with seller_ids, marketplace, item_ids, skus, fulfillment_type, and pagination. fulfillment_type filters the product's listing attribute only; it never splits or re-attributes units by channel. There is no sort_by / sort_order and no units range filter on this tool. Rows carry item_id, never a backend asin. Bucket keys match the Walmart P&L series tools for the same inputs. Do not invent seller_id / item_id / sku values, and do not mix with Amazon velocity. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_get_wmt_sales_velocity(args: {
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Optional inclusive start day in date_tz, YYYY-MM-DD (no time). Omit BOTH endpoints to default to the most recent 30 calendar days. Providing only one of the two endpoints is an error (INVALID_INPUT).
  current_date_from?: string | null;
  // Optional inclusive end day in date_tz, YYYY-MM-DD (no time). Omit BOTH endpoints to default to the most recent 30 calendar days. Providing only one of the two endpoints is an error (INVALID_INPUT). A value after today in date_tz is silently clamped to today in date_tz (the future has no data).
  current_date_to?: string | null;
  // Timezone used to interpret calendar dates. Bound to the Walmart marketplace (not any IANA timezone): today only America/Los_Angeles (US). Optional; defaults to America/Los_Angeles.
  date_tz?: "America/Los_Angeles";
  // Optional. Filter by the product's listing attribute: WFS (Walmart Fulfillment Services) | WFSE (WFS Eligible) | SF (Seller Fulfilled). Multi-select ORs within the array; omit / null = all three. NOTE: this matches the product's fulfillment attribute — filtered rows may STILL show non-zero quantities on the other channel (e.g. fulfillment_type=['WFS'] can return rows with sf_available > 0), because one Walmart SKU can hold WFS and SF stock at the same time.
  fulfillment_type?: Array<string> | null;
  // Optional time-bucket size: day | week | month. Omit to default to 'day'. Week/month align to calendar boundaries (week starts Monday; month on the 1st); first/last buckets may be partial vs the requested range. Bucket keys match the Walmart P&L series tools for the same inputs.
  granularity?: "day" | "week" | "month";
  // Optional. Exact IN filter by Walmart item_id list (array of strings). Restricts which product rows are returned; the response is still one row per product. Default null (all items).
  item_ids?: Array<string> | null;
  // Optional Walmart marketplace filter as an array of country short codes. Only US is enabled today (CA / MX reserved); default null = all authorised Walmart marketplaces.
  marketplace?: Array<string> | null;
  // Optional. 1-based page number. Default 1.
  page_index?: number | null;
  // Optional. Rows per page, 5..1000. Default 50.
  page_size?: number | null;
  // Optional. Filter on Walmart seller accounts (array of partner_id strings). Intersected with the sellers authorised on the Pacvue token; no intersection → empty metrics object. Default null (all authorised Walmart sellers).
  seller_ids?: Array<string> | null;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Optional. Exact IN filter by seller SKU list (array of strings). Restricts which product rows are returned; the response is still one row per product. Default null (all SKUs).
  skus?: Array<string> | null;
}): Promise<CallToolResult<{
  // Walmart units-sold time series per SKU row, bucketed by the requested granularity. Not an inventory snapshot and not full P&L.
  data: {
  // 1-based page number echoed back.
  page_index?: number | null;
  // Rows per page echoed back.
  page_size?: number | null;
  // One row per SKU (× marketplace × seller). Empty array when no product matches or no authorised Walmart sellers match.
  rows: Array<{
  // Product brand; may be null.
  brand?: string | null;
  // The product's listing attribute: 'WFS' | 'WFSE' | 'SF'. Units sold are NOT split or re-attributed by fulfillment channel — one SKU has one sales series regardless of its WFS/SF inventory split.
  fulfillment_type?: string | null;
  // Product image URL, when known.
  image_url?: string | null;
  // Walmart item_id of this product (never a backend asin).
  item_id?: string | null;
  // Marketplace country short code; today always 'US'.
  marketplace?: string | null;
  // Product listing title.
  product_name?: string | null;
  // Units-sold time series for this SKU (values map only).
  sales_velocity: {
  // Bucket-start date (yyyy-MM-dd, in date_tz) → units sold, zero-filled over the requested range. Bucket size comes from the request granularity and is never echoed as scope/offset. Week buckets start Monday, month buckets on the 1st, so the first key may precede current_date_from for a partial natural-calendar bucket. Values are non-negative unit counts. The metric is implicitly units_sold — there is no metric_key.
  values: { [key: string]: number | number; };
};
  // Walmart seller (partner_id) that owns this listing.
  seller_id?: string | null;
  // Seller SKU — the row grain.
  sku?: string | null;
}>;
  // Matching SKU rows after filters, before pagination (0 when empty).
  total_count?: number | null;
} | null;
  // Null on success; error object on failure.
  error?: {
  // Error code string, e.g. 'INVALID_INPUT.DATE_RANGE_INVALID'.
  code: string;
  // Additional error context keyed by field or topic.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Suggested action to resolve the error.
  remediation: string;
} | null;
  // Optional non-blocking advisory (spec §6.1). Present only when the platform attaches one — currently a quota near-limit reminder (code QUOTA_NEAR_LIMIT). Null/absent on the vast majority of responses. Independent of 'error': the call still succeeded.
  notice?: {
  // Notice code, e.g. 'QUOTA_NEAR_LIMIT'.
  code: string;
  // Human-readable advisory message for the end user.
  message: string;
  // Advisory severity, e.g. 'warning'.
  severity: string;
} | null;
}>>; };
```

## 90. `list_ads_rules`

Helium 10 List Ads Rules (Ads Rule, read, paginated). Lists live automation rules (source=live) or the draft box (source=draft) for this account. platform and rule_type are required -- ask the user which rule type to list and take both values from get_ads_rule_platforms (v1 supports amazon + rule_type=BID only). Optional filters: profile_id, name (fuzzy substring); paging: limit / offset. Returns {items, total, limit, offset}. Use source=draft to find a saved-but-not-committed draft to resume, and source=live to find an existing rule to edit or delete. source=live is tenant-scoped: pass profile_id, or omit it and the server auto-resolves the single accessible profile (returns a candidate list when several exist). A live rule that carries uneditable_reason (e.g. UNSUPPORTED.RULE_HAS_TACTIC, meaning it uses a Tactic) CANNOT be edited through the MCP -- it is still listed, but editing must be done on the Helium 10 Ads Rules & Automation page. Next: get_ads_rule(source, id) for full detail before editing/deleting. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_list_ads_rules(args: {
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Max items per page. Default 50, max 500.
  limit?: number;
  // Case-insensitive substring filter on the rule name.
  name?: string | null;
  // Zero-based paging offset. Default 0.
  offset?: number;
  // Platform key from get_ads_rule_platforms. Required. v1 supports amazon only (walmart planned). Case-sensitive.
  platform: string;
  // Target profile_id (from get_ads_query_materials). Optional for source=draft; for source=live it scopes the tenant -- omit it only if you want the server to auto-resolve your single accessible profile.
  profile_id?: string | null;
  // Rule type from get_ads_rule_platforms. Required -- ask the user which rule type to list. v1 supports BID only. Case-sensitive.
  rule_type: string;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Which collection to list: 'live' = committed automation rules; 'draft' = the draft box (saved, not yet committed).
  source: string;
}): Promise<CallToolResult<{ [key: string]: unknown; }>>; };
```

## 91. `list_amazon_cogs`

Return a paginated list of CONFIGURED Amazon COGS records. One row = one COGS configuration (not one SKU); a SKU appears on multiple rows when cogs_type=by_period (non-overlapping effective periods). Each row has cogs_id (for delete), product identity (product_name, asin, sku, brand, image_url, product_page_url, seller_id, marketplace, fulfillment_type, sku_status) and COGS fields (cogs_type, currency, product_cost, shipping_cost, tariffs, total_cogs, starts_at, ends_at). No required input. Use to view / audit / search configured unit costs, or to get cogs_id before delete_amazon_cogs. NOT for actual COGS dollars in P&L (use get_product_profit_and_loss_*; cost_of_goods=0 there may mean COGS not configured). Only SKUs WITH configured COGS are returned. Narrow with seller_ids, marketplace (short country codes US/CA/...), asins, skus, fulfillment_type (FBA|FBM), cogs_type (by_period|persistent), sku_status (active|inactive), value range filters, or effective-date endpoint filters; sort with sort_by + sort_order. Values compared as stored (no FX conversion across marketplaces). This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_list_amazon_cogs(args: { asins?: Array<string> | null; cogs_type?: "by_period" | "persistent" | null; context?: string; ends_at_max?: string | null; ends_at_min?: string | null; fulfillment_type?: "FBA" | "FBM" | null; marketplace?: Array<string> | null; page_index?: number | null; page_size?: number | null; product_cost_max?: number | null; product_cost_min?: number | null; seller_ids?: Array<string> | null; session_id?: string; shipping_cost_max?: number | null; shipping_cost_min?: number | null; sku_status?: "active" | "inactive" | null; skus?: Array<string> | null; sort_by?: "product_cost" | "shipping_cost" | "tariffs" | "total_cogs" | "starts_at" | "ends_at" | null; sort_order?: "asc" | "desc" | null; starts_at_max?: string | null; starts_at_min?: string | null; tariffs_max?: number | null; tariffs_min?: number | null; total_cogs_max?: number | null; total_cogs_min?: number | null; }): Promise<CallToolResult<{
  // Paginated configured COGS records. Null when no matching data.
  data: {
  // 1-based page number that was returned. Matches the page_index request parameter (defaults to 1 when not specified).
  page_index: number;
  // Maximum number of rows per page that was applied. Matches the page_size request parameter (defaults to 50 when not specified; range 5–1000).
  page_size: number;
  // COGS configuration rows for the current page (one row per COGS record).
  rows: Array<{
  // Amazon ASIN.
  asin?: string | null;
  // Brand name when known; null when unknown.
  brand?: string | null;
  // Opaque COGS record ID. Always present. Pass to delete_amazon_cogs. May change after overlay edits that split/rewrite rows — re-list before delete.
  cogs_id?: string | null;
  // COGS configuration type: by_period | persistent.
  cogs_type?: string | null;
  // ISO 4217 currency code (e.g. USD). Typically the marketplace default currency.
  currency?: string | null;
  // yyyy-MM-dd, or null for open-ended (effective into the future). Omitted for persistent.
  ends_at?: string | null;
  // SKU fulfillment channel: FBA | FBM. Optional sparse — omitted when the backend does not populate it.
  fulfillment_type?: string | null;
  // Primary product image URL.
  image_url?: string | null;
  // Short country code (e.g. US).
  marketplace?: string | null;
  // Unit product (procurement) cost.
  product_cost?: number | null;
  // Listing title.
  product_name?: string | null;
  // Amazon product detail page URL (marketplace-appropriate; supplied by the Profits backend).
  product_page_url?: string | null;
  // Amazon seller account ID.
  seller_id?: string | null;
  // Seller-borne inbound shipping cost (supplier -> destination warehouse).
  shipping_cost?: number | null;
  // Seller SKU.
  sku?: string | null;
  // Amazon listing status on the SKU: active | inactive. Optional sparse — omitted when unknown/empty on backend.
  sku_status?: string | null;
  // yyyy-MM-dd. Effective start date. Omitted for persistent.
  starts_at?: string | null;
  // Tariff / duty cost component.
  tariffs?: number | null;
  // product_cost + shipping_cost + tariffs (server-enforced invariant).
  total_cogs?: number | null;
}>;
  // Total number of matching product rows across all pages for the given filters. Use this value together with page_size to determine the total number of pages: ceil(total_count / page_size).
  total_count: number;
} | null;
  // Null on success; error on failure.
  error?: {
  // Error code string, e.g. 'INVALID_INPUT.DATE_RANGE_INVALID'.
  code: string;
  // Additional error context keyed by field or topic.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Suggested action to resolve the error.
  remediation: string;
} | null;
  // Optional non-blocking advisory (spec §6.1). Present only when the platform attaches one — currently a quota near-limit reminder (code QUOTA_NEAR_LIMIT). Null/absent on the vast majority of responses. Independent of 'error': the call still succeeded.
  notice?: {
  // Notice code, e.g. 'QUOTA_NEAR_LIMIT'.
  code: string;
  // Human-readable advisory message for the end user.
  message: string;
  // Advisory severity, e.g. 'warning'.
  severity: string;
} | null;
}>>; };
```

## 92. `list_amazon_indirect_cost_settings`

Return a paginated list of Amazon Indirect Cost SETTINGS (the configuration / 'contract' rows), one row = one setting. This is the CRUD/management surface: use it to view, search, or audit configured Indirect Cost rules and — importantly — to get the cost_id needed by edit_amazon_indirect_cost and delete_amazon_indirect_costs. Each row has cost_id, cost_name, cost_level (h10_account|seller|marketplace|product), frequency, amount (contract total), currency, amortize_daily, starts_at, ends_at (null when open-ended), created_at, and — sparse by level — seller_id, marketplace (short country code), and a nested skus[] array (product level only) carrying per-SKU product identity (sku, asin, product_name, brand, image_url, product_page_url, fulfillment_type, sku_status). No required input. Narrow with seller_ids, marketplace (short codes US/CA/...), skus, cost_level, frequency, cost_name (case-sensitive substring), amount_min/max, created_at_min/max, starts_at_min/max, ends_at_min/max; sort with sort_by (amount | created_at | starts_at | ends_at) + sort_order. NOT for the expanded day-level charges/'bills' (use list_amazon_indirect_costs), NOT for aggregated P&L indirect_cost (use the P&L tools — indirect_cost=0 there may mean unset), and NOT for COGS (use list_amazon_cogs). Amazon only. Filtering by seller_ids / marketplace / skus excludes settings that lack that scope (an h10_account setting never matches a seller_ids filter), so an empty result under a seller_ids filter does NOT prove nothing is configured — query cost_level=h10_account as well before concluding that. A non-empty result means it IS configured; then check whether starts_at/ends_at actually cover the window you are asking about. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_list_amazon_indirect_cost_settings(args: {
  // Inclusive upper bound on the setting amount (> 0). Compared as stored (no FX). Default: null.
  amount_max?: number | null;
  // Inclusive lower bound on the setting amount (> 0). Compared as stored (no FX). Default: null.
  amount_min?: number | null;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Filter by inferred level: h10_account | seller | marketplace | product. Omit = all.
  cost_level?: "h10_account" | "seller" | "marketplace" | "product" | null;
  // Case-sensitive substring match (LIKE) on the setting name. Omit = no name filter.
  cost_name?: string | null;
  // yyyy-MM-dd. Keep settings whose created_at calendar day is <= this (inclusive).
  created_at_max?: string | null;
  // yyyy-MM-dd. Keep settings whose created_at calendar day is >= this (inclusive).
  created_at_min?: string | null;
  // yyyy-MM-dd. Keep settings whose ends_at is <= this (inclusive). Open-ended settings (ends_at null) never match an ends_at_* filter.
  ends_at_max?: string | null;
  // yyyy-MM-dd. Keep settings whose ends_at is >= this (inclusive). Open-ended settings (ends_at null) never match an ends_at_* filter.
  ends_at_min?: string | null;
  // Filter by frequency: one_time | daily | weekly | monthly | quarterly | half_yearly | annually. Omit = all.
  frequency?: "one_time" | "daily" | "weekly" | "monthly" | "quarterly" | "half_yearly" | "annually" | null;
  // Optional marketplace filter as an array of upper-case region short codes (e.g. ['US', 'CA', 'MX']). Case-sensitive — lower-case codes are rejected. Allowed: AE, AU, BE, BR, CA, CN, DE, EG, ES, FR, IE, IN, IT, JP, MX, NL, PL, SA, SE, SG, TR, UK, US, ZA. Default null (all authorised marketplaces).
  marketplace?: Array<string> | null;
  // Optional. Page number (integer), starts from 1. Default 1.
  page_index?: number | null;
  // Optional. Rows per page (integer). Min 5, max 1000. Default 50.
  page_size?: number | null;
  // Optional. SQL IN filter on seller account IDs (array of strings). Profits intersects with sellers from Pacvue token; no intersection → empty result. Default null (all authorised sellers).
  seller_ids?: Array<string> | null;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Optional. SQL IN filter by seller SKU list (array of strings). Default null (all SKUs).
  skus?: Array<string> | null;
  // Field to sort by: amount | created_at | starts_at | ends_at. Omit = server default.
  sort_by?: "amount" | "created_at" | "starts_at" | "ends_at" | null;
  // Sort direction: asc | desc (default desc). Ignored when sort_by is omitted.
  sort_order?: "asc" | "desc" | null;
  // yyyy-MM-dd. Keep settings whose starts_at is <= this (inclusive).
  starts_at_max?: string | null;
  // yyyy-MM-dd. Keep settings whose starts_at is >= this (inclusive).
  starts_at_min?: string | null;
}): Promise<CallToolResult<{
  // Paginated Indirect Cost settings. Null when no matching data.
  data: {
  // 1-based page number that was returned. Matches the page_index request parameter (defaults to 1 when not specified).
  page_index: number;
  // Maximum number of rows per page that was applied. Matches the page_size request parameter (defaults to 50 when not specified; range 5–1000).
  page_size: number;
  // Indirect Cost setting rows for the current page (one row = one setting). Empty list when no settings match.
  rows: Array<{
  // Amortize flag (null backend → false).
  amortize_daily?: boolean | null;
  // Setting amount (> 0). Total, split across SKUs when expanded.
  amount?: number | null;
  // Opaque setting id. Always present. Pass to edit_amazon_indirect_cost / delete_amazon_indirect_costs.
  cost_id?: string | null;
  // Inferred level: h10_account | seller | marketplace | product. Always present.
  cost_level?: string | null;
  // Setting name (unique per H10 account).
  cost_name?: string | null;
  // ISO-8601 create timestamp (e.g. 2026-07-20T23:37:38). Always present.
  created_at?: string | null;
  // ISO 4217 currency code.
  currency?: string | null;
  // Effective end date yyyy-MM-dd, or null/omitted for open-ended.
  ends_at?: string | null;
  // one_time | daily | weekly | monthly | quarterly | half_yearly | annually.
  frequency?: string | null;
  // Short country code (e.g. US). Sparse — omitted for h10_account / seller.
  marketplace?: string | null;
  // Amazon seller account ID. Sparse — omitted for h10_account.
  seller_id?: string | null;
  // Attached SKUs. Sparse — only present at product level.
  skus?: Array<{
  // Amazon ASIN when known.
  asin?: string | null;
  // Brand name when known; null when unknown.
  brand?: string | null;
  // SKU fulfillment channel: FBA | FBM. Optional sparse.
  fulfillment_type?: string | null;
  // Primary product image URL.
  image_url?: string | null;
  // Listing title.
  product_name?: string | null;
  // Amazon product detail page URL (marketplace-appropriate). Sparse.
  product_page_url?: string | null;
  // Seller SKU. Always present on product-level settings.
  sku?: string | null;
  // Amazon listing status: active | inactive. Optional sparse.
  sku_status?: string | null;
}> | null;
  // Effective start date, yyyy-MM-dd.
  starts_at?: string | null;
}>;
  // Total number of matching product rows across all pages for the given filters. Use this value together with page_size to determine the total number of pages: ceil(total_count / page_size).
  total_count: number;
} | null;
  // Null on success; error on failure.
  error?: {
  // Error code string, e.g. 'INVALID_INPUT.DATE_RANGE_INVALID'.
  code: string;
  // Additional error context keyed by field or topic.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Suggested action to resolve the error.
  remediation: string;
} | null;
  // Optional non-blocking advisory (spec §6.1). Present only when the platform attaches one — currently a quota near-limit reminder (code QUOTA_NEAR_LIMIT). Null/absent on the vast majority of responses. Independent of 'error': the call still succeeded.
  notice?: {
  // Notice code, e.g. 'QUOTA_NEAR_LIMIT'.
  code: string;
  // Human-readable advisory message for the end user.
  message: string;
  // Advisory severity, e.g. 'warning'.
  severity: string;
} | null;
}>>; };
```

## 93. `list_amazon_indirect_costs`

Return a paginated list of EXPANDED Amazon Indirect Cost CHARGES — the materialized 'bills' derived from Indirect Cost settings (settings ≈ contract; charges ≈ the monthly bills it generates). One row = one occurrence_date × one cost_name; a product-level multi-SKU setting still yields one row per date, with per-SKU split amounts nested under skus[]. Each row carries the setting context (cost_name, cost_level, frequency, currency, amortize_daily, amount, starts_at, ends_at) plus occurrence_date and occurrence_amount. No charge-row ids and no cost_id are exposed — manage configuration via list_amazon_indirect_cost_settings + the write tools. No required input. Use this to VERIFY how a setting lands on the calendar (daily amort vs discrete period points) or reconcile P&L indirect_cost. Narrow with seller_ids, marketplace (short country codes US/CA/...), skus, cost_level, frequency, cost_name, occurrence_date_min/max, occurrence_amount_min/max; sort with sort_by (occurrence_date | occurrence_amount) + sort_order. Dates beyond the backend's ~12-month materialization horizon simply return no rows (not an error). cost_level is h10_account | seller | marketplace | product. Filtering by seller_ids / marketplace / skus excludes charges whose setting lacks that scope — an h10_account-level setting never matches a seller_ids filter, so an empty result under seller_ids does NOT prove nothing is configured; query cost_level=h10_account as well before concluding that. NOT for browsing/managing settings (use list_amazon_indirect_cost_settings and the write tools) or for aggregated P&L indirect_cost (use the P&L tools — indirect_cost=0 there may mean unset); Amazon only. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_list_amazon_indirect_costs(args: {
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Filter by inferred level: h10_account | seller | marketplace | product. Omit = all.
  cost_level?: "h10_account" | "seller" | "marketplace" | "product" | null;
  // Case-sensitive substring match (LIKE) on the setting name. Omit = no name filter.
  cost_name?: string | null;
  // Filter by setting frequency: one_time | daily | weekly | monthly | quarterly | half_yearly | annually. Omit = all.
  frequency?: "one_time" | "daily" | "weekly" | "monthly" | "quarterly" | "half_yearly" | "annually" | null;
  // Optional marketplace filter as an array of upper-case region short codes (e.g. ['US', 'CA', 'MX']). Case-sensitive — lower-case codes are rejected. Allowed: AE, AU, BE, BR, CA, CN, DE, EG, ES, FR, IE, IN, IT, JP, MX, NL, PL, SA, SE, SG, TR, UK, US, ZA. Default null (all authorised marketplaces).
  marketplace?: Array<string> | null;
  // Inclusive upper bound on a row's occurrence_amount (> 0). Compared as stored (no FX). Default: null (no bound).
  occurrence_amount_max?: number | null;
  // Inclusive lower bound on a row's occurrence_amount (> 0). Compared as stored (no FX). Default: null (no bound).
  occurrence_amount_min?: number | null;
  // yyyy-MM-dd. Keep charges whose occurrence_date is <= this day (inclusive).
  occurrence_date_max?: string | null;
  // yyyy-MM-dd. Keep charges whose occurrence_date is >= this day (inclusive).
  occurrence_date_min?: string | null;
  // Optional. Page number (integer), starts from 1. Default 1.
  page_index?: number | null;
  // Optional. Rows per page (integer). Min 5, max 1000. Default 50.
  page_size?: number | null;
  // Optional. SQL IN filter on seller account IDs (array of strings). Profits intersects with sellers from Pacvue token; no intersection → empty result. Default null (all authorised sellers).
  seller_ids?: Array<string> | null;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Optional. SQL IN filter by seller SKU list (array of strings). Default null (all SKUs).
  skus?: Array<string> | null;
  // Field to sort rows by: occurrence_date | occurrence_amount. Omit = server default.
  sort_by?: "occurrence_date" | "occurrence_amount" | null;
  // Sort direction: asc | desc (default desc). Ignored when sort_by is omitted.
  sort_order?: "asc" | "desc" | null;
}): Promise<CallToolResult<{
  // Paginated expanded Indirect Cost charges. Null when no matching data.
  data: {
  // 1-based page number that was returned. Matches the page_index request parameter (defaults to 1 when not specified).
  page_index: number;
  // Maximum number of rows per page that was applied. Matches the page_size request parameter (defaults to 50 when not specified; range 5–1000).
  page_size: number;
  // Expanded charge rows for the current page. One row = one occurrence_date × cost_name. Empty list when no charges match.
  rows: Array<{
  // Setting amortize flag (null backend → false).
  amortize_daily?: boolean | null;
  // Setting configured amount (contract total), for audit context.
  amount?: number | null;
  // Inferred level: h10_account | seller | marketplace | product.
  cost_level?: string | null;
  // Setting name.
  cost_name?: string | null;
  // ISO 4217 currency code.
  currency?: string | null;
  // Setting end date yyyy-MM-dd, or null/omitted for open-ended.
  ends_at?: string | null;
  // Setting frequency: one_time | daily | weekly | monthly | quarterly | half_yearly | annually.
  frequency?: string | null;
  // Short country code (e.g. US). Sparse — omitted for h10_account / seller.
  marketplace?: string | null;
  // Amount for this occurrence_date × cost_name. For multi-SKU product rows, equals the sum of skus[].occurrence_amount.
  occurrence_amount?: number | null;
  // yyyy-MM-dd — the day this charge lands.
  occurrence_date?: string | null;
  // Amazon seller account ID. Sparse — omitted for h10_account.
  seller_id?: string | null;
  // Per-SKU split for product-level charges. Sparse — only present at product level.
  skus?: Array<{
  // Amazon ASIN when known.
  asin?: string | null;
  // Brand name when known; null when unknown.
  brand?: string | null;
  // SKU fulfillment channel: FBA | FBM. Optional sparse.
  fulfillment_type?: string | null;
  // Primary product image URL.
  image_url?: string | null;
  // This SKU's share of the parent row's occurrence_amount for the day.
  occurrence_amount?: number | null;
  // Listing title.
  product_name?: string | null;
  // Amazon product detail page URL (marketplace-appropriate). Sparse.
  product_page_url?: string | null;
  // Seller SKU.
  sku?: string | null;
  // Amazon listing status: active | inactive. Optional sparse.
  sku_status?: string | null;
}> | null;
  // Setting start date, yyyy-MM-dd.
  starts_at?: string | null;
}>;
  // Total number of matching product rows across all pages for the given filters. Use this value together with page_size to determine the total number of pages: ceil(total_count / page_size).
  total_count: number;
} | null;
  // Null on success; error on failure.
  error?: {
  // Error code string, e.g. 'INVALID_INPUT.DATE_RANGE_INVALID'.
  code: string;
  // Additional error context keyed by field or topic.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Suggested action to resolve the error.
  remediation: string;
} | null;
  // Optional non-blocking advisory (spec §6.1). Present only when the platform attaches one — currently a quota near-limit reminder (code QUOTA_NEAR_LIMIT). Null/absent on the vast majority of responses. Independent of 'error': the call still succeeded.
  notice?: {
  // Notice code, e.g. 'QUOTA_NEAR_LIMIT'.
  code: string;
  // Human-readable advisory message for the end user.
  message: string;
  // Advisory severity, e.g. 'warning'.
  severity: string;
} | null;
}>>; };
```

## 94. `list_my_products`

Return a paginated list of the caller's OWN Amazon product rows (one row per SKU x marketplace x seller). Each row carries product identity only: product_name, asin, sku, brand, image_url, product_page_url, seller_id, marketplace, fulfillment_type. No P&L, inventory, or listing-analysis metrics. No required input — default scope is all sellers authorised by the Pacvue token; call with no arguments to page through the full authorised catalog. Use when the user asks to list, browse, or find their own products/SKUs/ASINs across linked seller accounts, wants the product info / details for a SPECIFIC ASIN or SKU in their own catalog (e.g. 'look up the product info for ASIN B0…' — pass it via asins/skus), needs product metadata (title, image, Amazon listing URL) before calling P&L or inventory tools, or wants to resolve which SKUs match a name/brand within their catalog. Narrow with seller_ids, marketplace, asins, skus, fulfillment_type (FBA | FBM), product_name (case-insensitive LIKE on title), or brand_name (case-insensitive LIKE on brand). Not for: market-wide product or competitor research (use search_products / search_inactive_products); comparing your own listing against competitor ASINs (use compare_listings — NOT this tool for a plain single-ASIN info lookup); profit & loss metrics (use get_product_profit_and_loss_summary); inventory on-hand / inbound quantities (use get_inventory_values); deep single-ASIN listing profile / BSR / scores (use get_listing_details). Does not distinguish active vs inactive listing status; rows reflect whatever Profits has ingested. No sort parameters — order is backend-defined. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_list_my_products(args: {
  // Optional. SQL IN filter by ASIN list (array of strings, e.g. ['B001234567']). Default null (all ASINs).
  asins?: Array<string> | null;
  // Optional. Case-insensitive substring match (SQL LIKE '%keyword%') against the brand name. Omit or null = no brand filter (rows with brand: null may still be returned).
  brand_name?: string | null;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Optional fulfillment channel filter: FBA | FBM. Omit to query both FBA and FBM inventory.
  fulfillment_type?: "FBA" | "FBM" | null;
  // Optional marketplace filter as an array of region short codes ONLY (e.g. ['US', 'CA', 'MX']); Amazon SP-API marketplace IDs (e.g. 'ATVPDKIKX0DER'), seller IDs, and domains are rejected. Allowed: AE, AU, BE, BR, CA, CN, DE, EG, ES, FR, IE, IN, IT, JP, MX, NL, PL, SA, SE, SG, TR, UK, US, ZA. Default null (all authorised marketplaces).
  marketplace?: Array<string> | null;
  // Optional. Page number (integer), starts from 1. Default 1.
  page_index?: number | null;
  // Optional. Rows per page (integer). Min 5, max 1000. Default 50.
  page_size?: number | null;
  // Optional. Case-insensitive substring match (SQL LIKE '%keyword%') against the product listing title. Omit or null = no title filter (rows with any title, including empty, may be returned).
  product_name?: string | null;
  // Optional. SQL IN filter on seller account IDs (array of strings). Profits intersects with sellers from Pacvue token; no intersection → empty result. Default null (all authorised sellers).
  seller_ids?: Array<string> | null;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Optional. SQL IN filter by seller SKU list (array of strings). Default null (all SKUs).
  skus?: Array<string> | null;
}): Promise<CallToolResult<{
  // Paginated list of the caller's own Amazon product catalog rows. Null when no matching data exists (e.g. seller_ids filter yields no authorised sellers).
  data: {
  // 1-based page number that was returned. Matches the page_index request parameter (defaults to 1 when not specified).
  page_index: number;
  // Maximum number of rows per page that was applied. Matches the page_size request parameter (defaults to 50 when not specified; range 5–1000).
  page_size: number;
  // Product rows for the current page, each containing the nine identity fields (product_name, asin, sku, brand, image_url, product_page_url, seller_id, marketplace, fulfillment_type). Grain = SKU × marketplace × seller. Empty list when no products match the filters.
  rows: Array<{
  // Amazon Standard Identification Number (ASIN), e.g. 'B088ZQMCVH'.
  asin?: string | null;
  // Brand name when known; null when not available.
  brand?: string | null;
  // Fulfillment channel for this SKU row: 'FBA' (Fulfilled by Amazon) or 'FBM' (Fulfilled by Merchant). One SKU is either FBA or FBM; one ASIN may appear on multiple rows when different SKUs use different channels.
  fulfillment_type?: string | null;
  // Primary product image URL served by Amazon. Null when no image is available.
  image_url?: string | null;
  // Amazon marketplace short country code where this product is sold (e.g. 'US', 'CA', 'UK'). Same vocabulary as the marketplace filter parameter.
  marketplace?: string | null;
  // Amazon product listing title. May be null when metadata is unavailable.
  product_name?: string | null;
  // Canonical Amazon product detail page URL for this row's marketplace + ASIN, supplied by the Profits backend (not assembled by the MCP gateway).
  product_page_url?: string | null;
  // Amazon seller account ID (merchant token) that owns this listing, e.g. 'A2R7UX9BFSMS61'. Corresponds to the seller_ids filter parameter.
  seller_id?: string | null;
  // Seller-defined SKU, unique within a seller account.
  sku?: string | null;
}>;
  // Total number of matching product rows across all pages for the given filters. Use this value together with page_size to determine the total number of pages: ceil(total_count / page_size).
  total_count: number;
} | null;
  // Null on success; error object on failure.
  error?: {
  // Error code string, e.g. 'INVALID_INPUT.DATE_RANGE_INVALID'.
  code: string;
  // Additional error context keyed by field or topic.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Suggested action to resolve the error.
  remediation: string;
} | null;
  // Optional non-blocking advisory (spec §6.1). Present only when the platform attaches one — currently a quota near-limit reminder (code QUOTA_NEAR_LIMIT). Null/absent on the vast majority of responses. Independent of 'error': the call still succeeded.
  notice?: {
  // Notice code, e.g. 'QUOTA_NEAR_LIMIT'.
  code: string;
  // Human-readable advisory message for the end user.
  message: string;
  // Advisory severity, e.g. 'warning'.
  severity: string;
} | null;
}>>; };
```

## 95. `list_tracked_competitor_keywords`

Helium 10 Competitor Keyword Rank Tracker. For a main ASIN with competitor ASINs configured in Keyword Tracker, returns the tracked keywords with your rank, each competitor's rank, relative_rank, rank_avg, and Competitor Performance Score (CPS) side-by-side. Use to find rank gaps on shared keywords, prioritize which keywords deserve PPC or listing investment, and monitor whether competitor moves are eroding your visibility. Not for: a single-ASIN view (use list_tracked_keywords); discovering new keywords (use get_keywords_new_suggestion); competitors not yet in Keyword Tracker (add them there first). Errors: invalid_input / invalid_asin / no_result / no_data / rate_limited / upstream_timeout. Returns up to `limit` rows per call (default 100, max 500); one page inlines at most ~500 rows / ~1MB, and oversized pages are truncated and continue via cursor. If data.pagination.has_more is true the result is incomplete: call again with `cursor` = data.pagination.next_cursor, but prefer narrowing filters over paginating through everything. Cursors expire 30 minutes after being issued and are bound to the query that issued them. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_list_tracked_competitor_keywords(args: { asin_or_url: string; boosted?: boolean | null; context?: string; cursor?: string | null; limit?: number; marketplace?: "US" | "CA" | "MX" | "BR" | "DE" | "ES" | "FR" | "IT" | "UK" | "NL" | "TR" | "IN" | "AE" | "EG" | "SA" | "SE" | "PL" | "JP" | "AU" | "SG" | "CN" | "BE" | "ZA" | "IE" | null; session_id?: string; sort_direction?: "asc" | "desc" | null; sort_field?: "keywords_phrase" | "search_volume" | "organic_rank" | "sponsored_rank" | null; tags?: Array<string> | null; }): Promise<CallToolResult<{
  // Keyword Tracker tool payload when the call completes; interpret together with ``code``.
  data?: {
  // Marketplace short code (e.g. `US`, `UK`, `DE`) for the Amazon store this result applies to.
  marketplace?: string;
  // Pagination metadata for this page of rows. When has_more is true the result set is NOT complete — pass next_cursor back to fetch the next batch.
  pagination: {
  // Pagination consistency mode. 'live': every page queries live data (no snapshot); if the underlying data changes between pages, rows may shift across page boundaries - restart without `cursor` for a fresh view.
  consistency: string;
  // Rows delivered so far across this cursor chain, including this response (exported file rows count for download delivery).
  cumulative_count: number;
  // ISO-8601 UTC time when next_cursor stops being accepted; null when next_cursor is null. Expired cursors are rejected - restart from the first page by omitting `cursor`.
  cursor_expires_at: string | null;
  // True when more rows exist beyond this response.
  has_more: boolean;
  // Present only when the requested `limit` was adjusted: the value actually applied after clamping into [1, max].
  limit_applied?: number;
  // Present only when the requested `limit` was adjusted: the value as requested, before clamping.
  limit_requested?: number;
  // Opaque cursor for the next batch; pass it back verbatim as `cursor` with the same query arguments. Null when has_more is false. When truncation_reason is 'traversal_bound' this cursor is a terminal marker: passing it back returns a guidance error instead of rows - narrow the filters, and never restart from the first page to 'continue'.
  next_cursor: string | null;
  // Rows returned inline in this response (0 in download responses).
  returned_count: number;
  // Total rows matching the query, when known; null when the tail has not been reached yet (rely on has_more).
  total_count: number | null;
  // True when this response covers less than the requested window; see truncation_reason for why and how to continue.
  truncated: boolean;
  // Why truncated is true. 'size_limit': page cut to fit the inline response budget - continue with next_cursor. 'traversal_bound': this tool cannot paginate past its per-query row cap; next_cursor is a terminal marker (reusing it returns an error) - narrow the filters to reach the remaining rows, never restart from page 1. Null when truncated is false.
  truncation_reason: "size_limit" | "traversal_bound" | null;
};
  // One-sentence coverage note for this response (which rows are included and how to get the rest). Read it before concluding the data is complete.
  results_summary: string;
  // Keyword-level rows for this listing or account slice.
  rows: Array<{
  // Your main product’s Amazon Standard Identification Number for this comparison.
  asin?: string | null;
  // Boost status: hourly rank updates for a limited window. Enhances your keyword tracking with hourly updates for 10 days, offering detailed insights to optimize your strategy.
  boost?: {
  // Whether Boost is currently active for this keyword.
  enabled?: boolean | null;
  // When Boost was turned on (epoch milliseconds), when provided.
  enabled_time?: number | number | null;
} | null;
  // Competitor ASINs included when comparing rank for this keyword.
  competitors?: Array<string> | null;
  // Competitor Performance Index is a measure how successful the listed competitors are as a group for the given keyword.
  cps?: string | null;
  // Keyword or phrase used by customers in search for a product.
  keywords_phrase?: string | null;
  // Organic search position for your product on this keyword. null = not ranked (not "data missing").
  organic_rank?: string | null;
  // Average rank of all ASINs (competitors and main). ASINs that did not rank are ignored in this calculation.
  rank_avg?: number | number | null;
  // Rank of the main ASIN relative to its competitors. For example, if two (2) competitors are ranked better than the main ASIN the relative rank will be three (3).
  relative_rank?: string | null;
  // Estimated number of times this keyword is searched monthly on Amazon.
  search_volume?: number | number | null;
  // Change versus the prior measurement for the search-volume style metric (difference from the previous snapshot).
  search_volume_trend?: number | number | null;
  // Sponsored position last detected for your product on this keyword. null = not ranked (not "data missing").
  sponsored_rank?: string | null;
  // Tags you use in Keyword Tracker to organize and filter keywords.
  tags?: Array<{
  // Tag color in the UI, when specified.
  color?: string | null;
  // Internal tag identifier.
  id?: number | number | null;
  // Tag name shown in Keyword Tracker.
  title?: string | null;
}> | null;
}>;
  // Which Keyword Tracker capability produced this payload: tracked keywords, competitor comparison, or new suggestions.
  tool?: string;
} | null;
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
}>>; };
```

## 96. `list_tracked_keywords`

Helium 10 Keyword Tracker. Returns metrics for keywords ALREADY tracked in Keyword Tracker — by ASIN/product URL, or account-wide when the ASIN is omitted — with search volume + trend, organic & sponsored rank + trend, CPR, boost status, tags, and permission-gated keyword_sales and ABA shares. Use for monitoring rank movement, detecting rank drops/gains, assessing visibility after a PPC or listing change, and reviewing the full tracked set. Not for: untracked reverse search (use get_keywords_by_asin); discovering new keywords to add (use get_keywords_new_suggestion); competitor comparison (use list_tracked_competitor_keywords); rank history over time (use get_keywords_rank_history). Errors: invalid_input / invalid_asin / no_result / no_data / rate_limited / upstream_timeout. Returns up to `limit` rows per call (default 100, max 500); one page inlines at most ~500 rows / ~1MB, and oversized pages are truncated and continue via cursor. If data.pagination.has_more is true the result is incomplete: call again with `cursor` = data.pagination.next_cursor, but prefer narrowing filters over paginating through everything. Cursors expire 30 minutes after being issued and are bound to the query that issued them. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_list_tracked_keywords(args: { asin_or_url?: string | null; boosted?: boolean | null; context?: string; cursor?: string | null; limit?: number; marketplace?: "US" | "CA" | "MX" | "BR" | "DE" | "ES" | "FR" | "IT" | "UK" | "NL" | "TR" | "IN" | "AE" | "EG" | "SA" | "SE" | "PL" | "JP" | "AU" | "SG" | "CN" | "BE" | "ZA" | "IE" | null; session_id?: string; sort_direction?: "asc" | "desc" | null; sort_field?: "keywords_phrase" | "search_volume" | "competing_products" | "organic_rank" | "sponsored_rank" | "keyword_sales" | "cpr" | "suggested_ppc_bid" | "tags" | "date" | null; tags?: Array<string> | null; }): Promise<CallToolResult<{
  // Keyword Tracker tool payload when the call completes; interpret together with ``code``.
  data?: {
  // Marketplace short code (e.g. `US`, `UK`, `DE`) for the Amazon store this result applies to.
  marketplace?: string;
  // Pagination metadata for this page of rows. When has_more is true the result set is NOT complete — pass next_cursor back to fetch the next batch.
  pagination: {
  // Pagination consistency mode. 'live': every page queries live data (no snapshot); if the underlying data changes between pages, rows may shift across page boundaries - restart without `cursor` for a fresh view.
  consistency: string;
  // Rows delivered so far across this cursor chain, including this response (exported file rows count for download delivery).
  cumulative_count: number;
  // ISO-8601 UTC time when next_cursor stops being accepted; null when next_cursor is null. Expired cursors are rejected - restart from the first page by omitting `cursor`.
  cursor_expires_at: string | null;
  // True when more rows exist beyond this response.
  has_more: boolean;
  // Present only when the requested `limit` was adjusted: the value actually applied after clamping into [1, max].
  limit_applied?: number;
  // Present only when the requested `limit` was adjusted: the value as requested, before clamping.
  limit_requested?: number;
  // Opaque cursor for the next batch; pass it back verbatim as `cursor` with the same query arguments. Null when has_more is false. When truncation_reason is 'traversal_bound' this cursor is a terminal marker: passing it back returns a guidance error instead of rows - narrow the filters, and never restart from the first page to 'continue'.
  next_cursor: string | null;
  // Rows returned inline in this response (0 in download responses).
  returned_count: number;
  // Total rows matching the query, when known; null when the tail has not been reached yet (rely on has_more).
  total_count: number | null;
  // True when this response covers less than the requested window; see truncation_reason for why and how to continue.
  truncated: boolean;
  // Why truncated is true. 'size_limit': page cut to fit the inline response budget - continue with next_cursor. 'traversal_bound': this tool cannot paginate past its per-query row cap; next_cursor is a terminal marker (reusing it returns an error) - narrow the filters to reach the remaining rows, never restart from page 1. Null when truncated is false.
  truncation_reason: "size_limit" | "traversal_bound" | null;
};
  // One-sentence coverage note for this response (which rows are included and how to get the rest). Read it before concluding the data is complete.
  results_summary: string;
  // Keyword-level rows for this listing or account slice.
  rows: Array<{
  // Keyword metrics, including click & conversion rates for the top 3 ASINs; may be empty when the feature or plan does not expose it. Gating: Diamond+ plan AND Brand Registry required; null otherwise.
  aba_total_clicks_share?: string | null;
  // Gain valuable insights into the top 3 clicked products for each keyword; may be empty when the feature or plan does not expose it. Gating: Diamond+ plan AND Brand Registry required; null otherwise.
  aba_total_conver_share?: string | null;
  // Amazon Standard Identification Number for the product this row belongs to.
  asin?: string | null;
  // Boost status: hourly rank updates for a limited window. Enhances your keyword tracking with hourly updates for 10 days, offering detailed insights to optimize your strategy.
  boost?: {
  // Whether Boost is currently active for this keyword.
  enabled?: boolean | null;
  // When Boost was turned on (epoch milliseconds), when provided.
  enabled_time?: number | number | null;
} | null;
  // Estimated number of units that need to be sold over 8 days, resulting from a search for this keyword in order for the product to rank on top half of page one for this keyword.
  cpr?: number | number | null;
  // Estimated number of weekly sales (in number of units) attributed to each keyword. Gating: Platinum+ plan AND North America marketplace only; field absent otherwise.
  keyword_sales?: number | number | null;
  // Keyword or phrase used by customers in search for a product.
  keywords_phrase?: string | null;
  // Organic search position detected for that keyword(s) for the analyzed product. null = not ranked (not "data missing").
  organic_rank?: string | null;
  // Change versus the prior organic rank measurement (difference from yesterday or the last snapshot).
  organic_rank_trend?: number | number | null;
  // Estimated number of times this keyword is searched monthly on Amazon.
  search_volume?: number | number | null;
  // Change versus the prior measurement for the search-volume style metric (difference from the previous snapshot).
  search_volume_trend?: number | number | null;
  // Sponsored ad position last detected in customer searches. null = not ranked (not "data missing").
  sponsored_rank?: string | null;
  // Change versus the prior sponsored position measurement.
  sponsored_rank_trend?: number | number | null;
  // Tags you use in Keyword Tracker to organize and filter keywords.
  tags?: Array<{
  // Tag color in the UI, when specified.
  color?: string | null;
  // Internal tag identifier.
  id?: number | number | null;
  // Tag name shown in Keyword Tracker.
  title?: string | null;
}> | null;
}>;
  // Which Keyword Tracker capability produced this payload: tracked keywords, competitor comparison, or new suggestions.
  tool?: string;
} | null;
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
}>>; };
```

## 97. `list_user_markets`

List Helium 10 Market Tracker 360 (MT360) markets for the authenticated account. Returns paginated market rows (id, name, marketplaceId, marketplace site code such as US/UK/JP, type, status, revenue metrics already scaled for display (/100 except Japan marketplaceId 10), defaultDateFrom/defaultDateTo as yyyy-MM-dd when not null, access level, etc.) plus pagination links and meta.hasReadonly. Each row type is a DSA MarketTypeEnum integer: 1=KEYWORDS, 2=BRANDS, 3=ASINS, 4=AUTOMATIC, 5=SUBCATEGORIES. Use this first to discover market_id values before calling other MT360 tools. Returns all market types in one list (keywords, brands, subcategories, ASINs, automatic, etc.). Narrow only with search, marketplace_id (DSA integer enum: 1=US … 10=JP, 12=BR … 24=IE; there is no 11), or pagination. Not for: market-level analytics, product rows, or competitor data — those require market-specific endpoints after you pick a market from this list. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_list_user_markets(args: {
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // DSA Amazon marketplace ID filter. Allowed values: 1=US, 2=CA, 3=MX, 4=DE, 5=ES, 6=FR, 7=IT, 8=UK, 9=IN, 10=JP, 12=BR, 13=TR, 14=AU, 15=NL, 16=SG, 17=AE, 18=EG, 19=SA, 20=SE, 21=PL, 22=BE, 23=ZA, 24=IE. There is no 11. Omit to include all marketplaces.
  marketplace_id?: 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 12 | 13 | 14 | 15 | 16 | 17 | 18 | 19 | 20 | 21 | 22 | 23 | 24 | null;
  // 1-based page number for paginated market list.
  page?: number | null;
  // Number of markets per page (default 10 on DSA backend).
  per_page?: number | null;
  // When true, return only read-only shared markets; when false, owned markets only.
  readonly_markets?: boolean | null;
  // Search read-only markets by owner email, username, or market name.
  readonly_search?: string | null;
  // Case-insensitive market name search. Does not filter by market type.
  search?: string | null;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Sort field; prefix with '-' for descending (e.g. -createdAt, name).
  sort?: string | null;
}): Promise<CallToolResult<{
  // Paginated MT360 market list from DSA: Laravel resource shape with data (market rows including marketplaceId, marketplace site code US/UK/JP/…, type as integer, and revenue fields scaled /100 except Japan), links (pagination), and meta (e.g. hasReadonly). Each row type is a DSA MarketTypeEnum integer: 1=KEYWORDS, 2=BRANDS, 3=ASINS, 4=AUTOMATIC, 5=SUBCATEGORIES.
  data?: { [key: string]: unknown; } | null;
  error?: {
  // Error code string, e.g. 'INVALID_INPUT.PARAM_INVALID'.
  code: string;
  // Additional error context keyed by field or topic.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Suggested action to resolve the error.
  remediation: string;
} | null;
}>>; };
```

## 98. `manage_ads_rule`

Helium 10 Manage Ads Rule (Ads Rule, step 4 of 4, WRITE). All rule write operations in one tool, selected by `action`.

### Do NOT call this tool with empty or guessed params
This is step 4. Resolve every input FIRST via the earlier steps, and when the user is vague, ASK them — never invent platform / rule_type / ids or call an action just to see what it needs. Hard ordering before draft_open:
1. get_ads_rule_platforms → choose a supported (platform, rule_type). If the user didn't specify, ask which platform and rule_type they want.
2. get_ads_rule_schema(platform, rule_type) → the field catalog + skeleton you build the draft from.
3. get_ads_query_materials → resolve profile_id and campaign ids.
Calling draft_open without a known platform+rule_type is an error, not a way to discover them.

### Confirmation is REQUIRED before draft_commit and rule_delete
draft_commit materializes the draft as a live rule (automated=true auto-applies and WILL change bids; automated=false is suggest-only and will NOT change bids) and rule_delete permanently removes a live rule (irreversible). BEFORE either, you MUST present a readable summary of exactly what will change and obtain explicit user confirmation. There is no dry-run / preview. draft_open / draft_save / draft_discard do not need confirmation (they never touch live rules).

### Rules that use a Tactic cannot be edited via MCP
If a rule uses a Tactic, draft_open(mode=edit) and draft_commit on it return UNSUPPORTED.RULE_HAS_TACTIC (isError). Check uneditable_reason from get_ads_rule / list_ads_rules BEFORE building an edit; if set, do NOT open an edit draft, do NOT retry, and do NOT create a new rule as a substitute (that would run two bid rules on the same targets) — direct the user to the Helium 10 Ads Rules & Automation page. draft_open(mode=create), draft_save and rule_delete are unaffected.

### Actions and their required params
- draft_open (platform, rule_type, mode, profile_id; + source_rule_id when mode=edit): create a draft in the draft box. Does NOT create a real rule. mode=create starts blank; mode=edit back-fills from an existing rule. Returns {draft_id, revision:1, mode, boundRuleId}. rule_type is locked once open.
- draft_save (draft_id, draft): whole-replace the draft content (the generic rule document) → validate → persist, revision+1. The draft MUST contain rule_name, frequency (with a preset), and at least one criteria (each with an action.type). targets.campaign_ids MAY be empty; an empty campaign_ids means the rule is bound to NO campaign (it will not run against any campaign) — do NOT add a campaign just to pass validation, as that silently narrows the rule's scope. timezone and criteria[].lookback may be omitted — the server fills America/Los_Angeles and {length:14, exclude:2}. On failure returns error.details.issues[] to fix. Optional expected_revision for optimistic locking. Re-save as many times as needed.
- draft_commit (draft_id): commit the draft to a live rule (create→POST / edit→PUT). Returns {rule_id, isDraft:false}. CONFIRM FIRST.
- draft_discard (draft_id): delete the draft (does not affect live rules). Non-idempotent: a second call with the same draft_id returns not_found.
- rule_delete (platform, profile_id, rule_id): delete a live rule. Irreversible. CONFIRM FIRST.

Build the `draft` from the get_ads_rule_schema skeleton: metric / operator / action values come from the schema, numbers are actual values per unitHints (e.g. ACOS 12% → 0.12). Prerequisites, in order: get_ads_rule_platforms → get_ads_rule_schema → get_ads_query_materials (profile_id / campaign ids). This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_manage_ads_rule(args: {
  // Operation type. draft_open=create a draft (no real rule); draft_save=save draft content; draft_commit=commit the draft to a real rule (create→POST / edit→PUT, CONFIRM with the user first); draft_discard=delete the draft; rule_delete=delete a live rule (irreversible, CONFIRM first). Required params per action are in the tool description.
  action: string;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // The generic rule document (draft_save only, whole-replace). Values (metric/operator/action, units, constraints) come from get_ads_rule_schema; fill actual values per unitHints (e.g. ACOS 12% → 0.12). See the schema skeleton.
  draft?: { [key: string]: unknown; } | null;
  // Draft id returned by draft_open. Required for draft_save / draft_commit / draft_discard. Decimal string (e.g. 2089352922874187778).
  draft_id?: string | null;
  // Optimistic lock (draft_save / draft_commit): pass the last revision you saw to prevent overwriting a concurrent edit.
  expected_revision?: number | null;
  // draft_open only. create=blank new rule; edit=bind an existing rule and back-fill (requires source_rule_id).
  mode?: string | null;
  // Platform from get_ads_rule_platforms (e.g. amazon). Required for draft_open and rule_delete.
  platform?: string | null;
  // Target profile from get_ads_query_materials(level=profile). Required for draft_open and rule_delete; bound to the draft at open time.
  profile_id?: string | null;
  // Live rule id. Required for rule_delete. 32-character lowercase hex string (e.g. faa679c9b78e4b9fb8b452326f24dd00).
  rule_id?: string | null;
  // Rule type from get_ads_rule_platforms (e.g. BID). Required for draft_open only; locked once the draft is open.
  rule_type?: string | null;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // draft_open + mode=edit only: the existing rule_id (from get_ads_rule) to edit. Overwritten via PUT at commit. 32-character lowercase hex string (e.g. faa679c9b78e4b9fb8b452326f24dd00).
  source_rule_id?: string | null;
}): Promise<CallToolResult<{ data?: { [key: string]: unknown; } | null; error?: { [key: string]: unknown; } | null; }>>; };
```

## 99. `request_image_upload_url`

Helium 10 helper — requests presigned upload credentials so LOCAL image files are uploaded out-of-band, never base64'd into the conversation. Non-idempotent: each call mints fresh, single-use upload slots. Use whenever the user has local image files bound for an analyze / generate tool.

BEFORE CALLING — do NOT guess these; ground them in what the user actually has. If you can read the files off the user's filesystem, derive both from the directory listing instead of asking:
  • count = the EXACT number of images the user is uploading (1-5). If the user hasn't said how many (and you can't tell from their attachments or filesystem), ASK first. Do NOT default to 5.
  • content_type = the actual format: "image/jpeg" for .jpg/.jpeg, "image/png" for .png. One type per batch — if the files are mixed or you can't verify the extensions, ASK rather than assuming.

Returns items[{image_ref, upload_url, upload_method, required_headers, curl_command, max_bytes}], upload_page_url, expires_at. Then pick ONE path by your client's capability:
  • Shell / HTTP available and the file readable locally (Cursor / Claude Code):
      run items[].curl_command (replace @<LOCAL_FILE_PATH> with the real path) to PUT each file;
      a 200 means success.
  • Pure MCP client that cannot issue HTTP (Claude Desktop / ChatGPT):
      hand the user upload_page_url (one for the whole batch); they upload all N files in the
      browser and paste back the N image_ref values.

Pass items[].image_ref to the downstream tool's `image_refs`. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_request_image_upload_url(args: {
  // REQUIRED. MIME type matching the user's files: image/jpeg for .jpg/.jpeg, image/png for .png. One type per batch; if the files are mixed or unknown, ask the user rather than guessing — this value is signed into the presigned URL, so a mismatch makes the upload fail.
  content_type: "image/jpeg" | "image/png";
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Exact number of images the user is uploading (1-5). Use the real count from the user's files (read it off their filesystem when you can) — do NOT default to 5; ask the user only when you can't determine it yourself.
  count: number;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{
  expires_at: number;
  items: Array<{ curl_command: string; image_ref: string; max_bytes: number; required_headers?: { [key: string]: string; }; upload_method?: string; upload_url: string; }>;
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
  upload_page_url: string;
}>>; };
```

## 100. `retrieve_listing_by_asin`

Helium 10 Listing Builder — fetches the current Amazon catalog images and listing text for a target ASIN. Reads the live Amazon catalog (real-time snapshot). Returns `images` (list of {url, variant}) plus the current `product_name` / `item_highlight` / `bullet_points` / `description`. Use to pull a listing's current live copy and images as the working baseline, or to check what the Amazon catalog shows for an ASIN right now. Not for: reading or scoring a listing without rewriting its copy (use get_listing_details / get_listing_score). This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_retrieve_listing_by_asin(args: {
  // Amazon Standard Identification Number (ASIN). Must be exactly 10 alphanumeric characters, e.g. B08N5WRWNW.
  asin: string;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Marketplace short code (case-sensitive). Confirm the marketplace with the user once, before the first Listing Builder call of the session; every later Listing Builder tool reuses that same value — do NOT ask the user for it again once it is set. Supported values: US, CA, MX, DE, ES, IT, FR, UK, IN, NL, AU, JP, BE, BR.
  marketplace: "US" | "CA" | "MX" | "DE" | "ES" | "IT" | "FR" | "UK" | "IN" | "NL" | "AU" | "JP" | "BE" | "BR";
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{
  // Listing-retrieval payload: the current Amazon catalog images and text for the requested ASIN. Up to 5 of these images — chosen with the user, defaulting to the first 5 — serve as input to analyze_product_images.
  data: {
  // Amazon Standard Identification Number (10 uppercase alphanumeric characters).
  asin?: string | null;
  // Current Amazon catalog bullet points (typically up to 5 entries).
  bullet_points?: Array<string | null> | null;
  // Current Amazon catalog product description.
  description?: string | null;
  // Ordered Amazon catalog product images (MAIN slot first).
  images?: Array<{
  // Public Amazon image URL (.jpg / .jpeg / .png).
  url?: string | null;
  // Amazon image slot variant code (for example 'MAIN', 'PT01', 'PT02').
  variant?: string | null;
} | null> | null;
  // Amazon's Item Highlight — a short highlight phrase Amazon displays alongside the title to distinguish this item from similar listings.
  item_highlight?: string | null;
  // Current Amazon catalog product title. Under Amazon's current rule the title is concise (<=75 chars) with extra selling points in item_highlight (<=125 chars); do not 'fix' a short fetched title by lengthening it.
  product_name?: string | null;
};
  // No error on success.
  error: null;
} | {
  // No data when the tool fails.
  data: null;
  // Tool execution error payload. Present when isError is true.
  error: {
  // Stable MCP tool error code.
  code: string;
  // Structured diagnostic details safe for the agent to inspect.
  details: { [key: string]: unknown; } | null;
  // Concise human-readable error message.
  message: string;
  // Actionable guidance the agent can use to correct and retry.
  remediation: string | null;
};
}>>; };
```

## 101. `rewrite_listing_section`

Helium 10 Listing Builder — rewrites ONE section of an existing listing: `target` selects the title, a single bullet, or the description. Live LLM call, non-idempotent; output language is derived server-side from `marketplace`. `title` and `item_highlight` are a matched pair — a 'title' rewrite regenerates both in the same AI call. Amazon now splits the legacy single <=200-character title into TWO fields: `title` (concise, <=75 characters) and `item_highlight` (<=125 characters). A short ~75-character title is CORRECT and compliant - do NOT lengthen it, keyword-stuff it, or treat it as incomplete; extra selling points and keywords belong in item_highlight. The two fields together replace the old long title. Use for targeted refinement: strengthen one field with fresh keywords while leaving the rest of the listing untouched. Not for: a full regeneration (use generate_listing with a revised allocation). This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_rewrite_listing_section(args: { brand_name?: string | null; brand_voice?: string | null; bullets: Array<string>; context?: string; description?: string | null; item_highlight?: string | null; kps?: number | null; marketplace: "US" | "CA" | "MX" | "DE" | "ES" | "IT" | "FR" | "UK" | "IN" | "NL" | "AU" | "JP" | "BE" | "BR"; max_bullet_len?: number | null; min_bullet_len?: number | null; optional_keywords?: Array<string> | null; parent_request_id?: number | null; product_name?: string | null; rewrite_content: string; rufus_asins?: Array<string> | null; session_id?: string; target: "title" | "bullet" | "description"; target_keywords: Array<{ cps?: number | null; keyword_sources?: Array<string> | null; phrase: string; priority_score?: number | null; search_volume?: number | null; starred?: boolean | null; suggested?: boolean | null; }>; title: string; user_feedback?: string | null; }): Promise<CallToolResult<{
  // Rewrite payload: the newly rewritten text for one listing section, plus the original text for diff display.
  data: {
  // Original text that was rewritten (useful for showing a diff).
  original_content?: string | null;
  // The item_highlight text supplied in the request, before this rewrite (useful for showing a diff). Only populated when target is 'title'; null otherwise.
  original_item_highlight?: string | null;
  // Rewrite record id — internal plumbing (parent_request_id chaining / sync validation); never show it to the user.
  request_id?: number | null;
  // AI-rewritten text for the target section.
  rewritten_content?: string | null;
  // AI-rewritten Amazon Item Highlight, produced together with the title in the same call. Only populated when target is 'title'; null otherwise.
  rewritten_item_highlight?: string | null;
  // Section that was rewritten: 1=TITLE, 2=BULLET, 3=DESCRIPTION (RewriteTargetEnum).
  target?: number | null;
};
  // No error on success.
  error: null;
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
} | {
  // No data when the tool fails.
  data: null;
  // Tool execution error payload. Present when isError is true.
  error: {
  // Stable MCP tool error code.
  code: string;
  // Structured diagnostic details safe for the agent to inspect.
  details: { [key: string]: unknown; } | null;
  // Concise human-readable error message.
  message: string;
  // Actionable guidance the agent can use to correct and retry.
  remediation: string | null;
};
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
}>>; };
```

## 102. `search_alert_events`

Helium 10 Alerts — query listing-change alert events for a seller. Supports three modes: (1) recent events — omit dates, returns latest `limit` events; (2) date-range — provide start_time + end_time; (3) single-event lookup — provide fired_at timestamp. Optionally filter by ASIN or alert_types. Do NOT use for Buy Box ownership history; use search_buybox_history instead. Returns up to `limit` rows per call (default 20, max 100); one page inlines at most ~100 rows / ~1MB, and oversized pages are truncated and continue via cursor. If data.pagination.has_more is true the result is incomplete: call again with `cursor` = data.pagination.next_cursor, but prefer narrowing filters over paginating through everything. Cursors expire 30 minutes after being issued and are bound to the query that issued them. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_search_alert_events(args: {
  // Optional. Filter to specific alert type codes. Default: null (all types).
  alert_types?: Array<1 | 2 | 4 | 5 | 6 | 7 | 14 | 15 | 16 | 17 | 18 | 19 | 20 | 21 | 23 | 24 | 25 | 26 | 27 | 28 | 29 | 30 | 31 | 32 | 33> | null;
  // Optional. Restrict results to a single ASIN (10-char alphanumeric, e.g. B01LR5S6HK). Default: null (all ASINs).
  asin?: string | null;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Opaque pagination cursor from a previous response's pagination.next_cursor. Omit for the first page. Pass it back verbatim with the same query arguments (limit may change between pages); never construct or edit it. Cursors are reusable but expire 30 minutes after being issued (pagination.cursor_expires_at) and are bound to the query that issued them.
  cursor?: string | null;
  // End of date-range query (inclusive). Format: YYYY-MM-DD or YYYY-MM-DD HH:MM:SS (UTC). Required together with start_time for date-range mode.
  end_time?: string | null;
  // Unix timestamp (seconds, UTC) of a specific event to fetch. Use for single-event detail lookup; takes precedence over date-range mode.
  fired_at?: number | null;
  // Number of rows to return. Defaults to 20, max 100; out-of-range values are clamped rather than rejected. request only what the user needs — up to 100 rows per page come back inline (slightly larger asks are truncated and continue via `cursor`). When pagination.has_more is true, fetch the next batch with `cursor`. Do NOT probe with limit=1 to learn the dataset size — every page carries the same total_count/has_more metadata, so ask for the rows you actually need and read the metadata of that response.
  limit?: number;
  // Marketplace code: US, UK, DE, JP, etc.
  marketplace: "US" | "CA" | "MX" | "DE" | "ES" | "IT" | "FR" | "UK" | "IN" | "NL" | "AU" | "JP" | "AE" | "BR" | "SA";
  // Helium 10 seller identifier.
  seller_id: string;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Start of date-range query (inclusive). Format: YYYY-MM-DD or YYYY-MM-DD HH:MM:SS (UTC). Required together with end_time for date-range mode.
  start_time?: string | null;
}): Promise<CallToolResult<{
  // Alert events payload (paginated). Null on error.
  data: {
  // List of alert events matching the query, sorted by fired_at descending. Empty list when no events found.
  events?: Array<{
  // Alert type code (integer 1–33), e.g. 7 = Price changed.
  alert_type?: number | null;
  // Human-readable label for the alert type, e.g. 'Price changed'.
  alert_type_info?: string | null;
  // Amazon ASIN of the product that changed.
  asin?: string | null;
  // Date the event was recorded, format YYYY-MM-DD.
  event_date?: string | null;
  // Unix timestamp (seconds, UTC) when the event fired.
  fired_at?: number | null;
  // The field value after the change. Null when not applicable.
  new_value?: string | null;
  // The field value before the change. Null when not applicable.
  old_value?: string | null;
}>;
  // Pagination metadata for this page of rows. When has_more is true the result set is NOT complete — pass next_cursor back to fetch the next batch.
  pagination?: {
  // Pagination consistency mode. 'live': every page queries live data (no snapshot); if the underlying data changes between pages, rows may shift across page boundaries - restart without `cursor` for a fresh view.
  consistency: string;
  // Rows delivered so far across this cursor chain, including this response (exported file rows count for download delivery).
  cumulative_count: number;
  // ISO-8601 UTC time when next_cursor stops being accepted; null when next_cursor is null. Expired cursors are rejected - restart from the first page by omitting `cursor`.
  cursor_expires_at: string | null;
  // True when more rows exist beyond this response.
  has_more: boolean;
  // Present only when the requested `limit` was adjusted: the value actually applied after clamping into [1, max].
  limit_applied?: number;
  // Present only when the requested `limit` was adjusted: the value as requested, before clamping.
  limit_requested?: number;
  // Opaque cursor for the next batch; pass it back verbatim as `cursor` with the same query arguments. Null when has_more is false. When truncation_reason is 'traversal_bound' this cursor is a terminal marker: passing it back returns a guidance error instead of rows - narrow the filters, and never restart from the first page to 'continue'.
  next_cursor: string | null;
  // Rows returned inline in this response (0 in download responses).
  returned_count: number;
  // Total rows matching the query, when known; null when the tail has not been reached yet (rely on has_more).
  total_count: number | null;
  // True when this response covers less than the requested window; see truncation_reason for why and how to continue.
  truncated: boolean;
  // Why truncated is true. 'size_limit': page cut to fit the inline response budget - continue with next_cursor. 'traversal_bound': this tool cannot paginate past its per-query row cap; next_cursor is a terminal marker (reusing it returns an error) - narrow the filters to reach the remaining rows, never restart from page 1. Null when truncated is false.
  truncation_reason: "size_limit" | "traversal_bound" | null;
};
  // One-sentence coverage note for this response (which rows are included and how to get the rest). Read it before concluding the data is complete.
  results_summary?: string;
} | null;
  error: null;
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
} | {
  data: null;
  // Structured error response from io-api.
  error: {
  // MCP string error code.
  code: string;
  // Optional structured error details.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Optional human-readable remediation guidance (may be omitted).
  remediation?: string;
};
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
}>>; };
```

## 103. `search_amazon_brand_analytics`

Helium 10 Top Search Terms & Brand Analytics (formerly Black Box — ABA). Exports Amazon Brand Analytics (ABA) Top Search Terms: per-phrase search frequency rank, exact/broad search volume with trend, CPC range, the top-3 clicked ASINs with click & conversion share, combined clicked-product metrics, and share trend vs the prior period. Use to find which products consumers click for a term, identify click/conversion-share leaders, compare ABA periods week-over-week or month-over-month, and find which keywords a competitor ASIN appears in. Returns up to `limit` rows per call (default 20, max 10000); one page inlines at most ~500 rows / ~1MB, and requests far above that (>1000 rows or >~2MB) are delivered as a download link instead of inline rows. If data.pagination.has_more is true the result is incomplete: call again with `cursor` = data.pagination.next_cursor, but prefer narrowing filters over paginating through everything. Cursors expire 30 minutes after being issued and are bound to the query that issued them. Set response_format='download' when the user explicitly wants the data as a file. REQUIRES Brand Registry — non-Brand accounts get PERMISSION.DENIED. Not for: generic keyword search volume only (use search_amazon_keywords). This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_search_amazon_brand_analytics(args: { context?: string; cursor?: string | null; detail_level?: "summary" | "full"; export_format?: "json" | "csv"; fields?: Array<string> | null; filters?: { average_monthly_age_from?: number | null; average_monthly_age_to?: number | null; categories?: Array<string> | null; click_and_conversion_share_operator?: string | null; click_share_clicked_product_count_requirement?: number | null; click_share_comparison_operator?: "=" | ">" | ">=" | "<" | "<=" | null; click_share_threshold?: number | null; clicked_product_age_from?: number | null; clicked_product_age_to?: number | null; clicked_products_average_rating_from?: number | null; clicked_products_average_rating_to?: number | null; conversion_share_clicked_product_count_requirement?: number | null; conversion_share_comparison_operator?: "=" | ">" | ">=" | "<" | "<=" | null; conversion_share_threshold?: number | null; cost_per_click_from?: number | null; cost_per_click_to?: number | null; current_search_volume_from?: number | null; current_search_volume_to?: number | null; exact_title_match_products_count_from?: number | null; exact_title_match_products_count_to?: number | null; include_all?: number | null; include_any?: number | null; keyword?: string | null; period_from?: string | null; period_to?: string | null; phrase_exclude_keyword?: string | null; phrase_include_keyword?: string | null; products?: Array<string> | null; range_type?: "weekly" | "monthly" | null; results_number_from?: number | null; results_number_to?: number | null; search_frequency_rank_from?: number | null; search_frequency_rank_previous_from?: number | null; search_frequency_rank_previous_to?: number | null; search_frequency_rank_to?: number | null; search_frequency_rank_trend_from?: number | null; search_frequency_rank_trend_to?: number | null; search_volume_from?: number | null; search_volume_previous_from?: number | null; search_volume_previous_to?: number | null; search_volume_to?: number | null; search_volume_trend_from?: number | null; search_volume_trend_to?: number | null; total_clicked_products_click_share_from?: number | null; total_clicked_products_click_share_to?: number | null; total_clicked_products_click_share_trend_from?: number | null; total_clicked_products_click_share_trend_to?: number | null; total_clicked_products_conversion_share_from?: number | null; total_clicked_products_conversion_share_to?: number | null; total_clicked_products_conversion_share_trend_from?: number | null; total_clicked_products_conversion_share_trend_to?: number | null; total_clicked_products_revenue_from?: number | null; total_clicked_products_revenue_to?: number | null; total_clicked_products_review_count_from?: number | null; total_clicked_products_review_count_to?: number | null; total_clicked_products_sales_from?: number | null; total_clicked_products_sales_to?: number | null; words_count_from?: number | null; words_count_to?: number | null; }; limit?: number; marketplace?: "US" | "CA" | "MX" | "DE" | "ES" | "IT" | "FR" | "UK" | "IN" | "NL" | "AU" | "JP" | "AE" | "BR" | "SA"; response_format?: "auto" | "inline" | "download"; session_id?: string; sort_by?: "default" | "search_volume" | "-search_volume" | "search_frequency_rank" | "-search_frequency_rank" | "total_clicked_products_click_share" | "-total_clicked_products_click_share" | "total_clicked_products_conversion_share" | "-total_clicked_products_conversion_share" | "clicked_product_1_click_share" | "-clicked_product_1_click_share" | "clicked_product_2_click_share" | "-clicked_product_2_click_share" | "clicked_product_3_click_share" | "-clicked_product_3_click_share" | "clicked_product_1_amazon_standard_identification_number" | "-clicked_product_1_amazon_standard_identification_number" | "clicked_product_2_amazon_standard_identification_number" | "-clicked_product_2_amazon_standard_identification_number" | "clicked_product_3_amazon_standard_identification_number" | "-clicked_product_3_amazon_standard_identification_number" | "clicked_product_1_conversion_share" | "-clicked_product_1_conversion_share" | "clicked_product_2_conversion_share" | "-clicked_product_2_conversion_share" | "clicked_product_3_conversion_share" | "-clicked_product_3_conversion_share" | "total_clicked_products_sales" | "-total_clicked_products_sales" | "total_clicked_products_revenue" | "-total_clicked_products_revenue" | "total_clicked_products_review_count" | "-total_clicked_products_review_count" | "clicked_products_average_rating" | "-clicked_products_average_rating" | "clicked_products_average_monthly_age" | "-clicked_products_average_monthly_age" | "phrase" | "-phrase" | "cost_per_click" | "-cost_per_click" | "exact_title_match_products_count" | "-exact_title_match_products_count" | "results_number" | "-results_number" | "current_search_volume" | "-current_search_volume" | null; }): Promise<CallToolResult<{
  // Success payload; full schema in the top-level data property.
  data: { [key: string]: unknown; };
  // No error on success.
  error: null;
} | {
  // No data when the tool fails.
  data: null;
  // Error payload; full schema in the top-level error property.
  error: { [key: string]: unknown; };
}>>; };
```

## 104. `search_amazon_keywords`

Helium 10 Keyword Database Search (formerly Black Box — Search Keywords). Searches the FULL Helium 10 keyword database (not one ASIN's keywords) by filters (search-volume / impression range, word count, competing products, price, monthly revenue, reviews, rating, category, density, broad reach potential, and top-10 competitor distribution buckets). Returns paginated keyword rows with demand metrics, competitive landscape, and aggregated top-10 competitor profile data. Use for market-level keyword discovery, low-competition keyword mining, niche sizing by demand, and category keyword-landscape analysis — no ASIN or seed term needed. Returns up to `limit` rows per call (default 20, max 200); one page inlines at most ~500 rows / ~1MB, and oversized pages are truncated and continue via cursor. If data.pagination.has_more is true the result is incomplete: call again with `cursor` = data.pagination.next_cursor, but prefer narrowing filters over paginating through everything. Cursors expire 30 minutes after being issued and are bound to the query that issued them. Not for: ABA click/conversion share & top clicked products (use search_amazon_brand_analytics); product lists (use search_products / search_inactive_products). If the user asks for historical Search Volume for returned keywords, call get_keywords_search_volume_history once per selected phrase. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_search_amazon_keywords(args: { context?: string; cursor?: string | null; fields?: Array<string> | null; filters?: { age_from?: number | null; age_to?: number | null; best_month?: string | null; best_sellers_rank_from?: number | null; best_sellers_rank_to?: number | null; broad_reach_potential_from?: number | null; broad_reach_potential_to?: number | null; category?: Array<string> | null; exact_impression_count_last_30_days_from?: number | null; exact_impression_count_last_30_days_to?: number | null; exact_title_match_products_count_from?: number | null; exact_title_match_products_count_to?: number | null; last_30_days_sales_from?: number | null; last_30_days_sales_to?: number | null; monthly_revenue_from?: number | null; monthly_revenue_to?: number | null; phrase_exclude_keyword?: string | null; phrase_keyword?: string | null; price_from?: number | null; price_to?: number | null; results_number_from?: number | null; results_number_to?: number | null; revenue_type1_from?: number | null; revenue_type1_to?: number | null; revenue_type2_from?: number | null; revenue_type2_to?: number | null; revenue_type3_from?: number | null; revenue_type3_to?: number | null; reviews_number_from?: number | null; reviews_number_to?: number | null; reviews_number_type1_from?: number | null; reviews_number_type1_to?: number | null; reviews_number_type2_from?: number | null; reviews_number_type2_to?: number | null; reviews_number_type3_from?: number | null; reviews_number_type3_to?: number | null; reviews_rating_from?: number | null; reviews_rating_to?: number | null; reviews_rating_type1_from?: number | null; reviews_rating_type1_to?: number | null; reviews_rating_type2_from?: number | null; reviews_rating_type2_to?: number | null; reviews_rating_type3_from?: number | null; reviews_rating_type3_to?: number | null; seller_type?: Array<string> | null; sellers_number_from?: number | null; sellers_number_to?: number | null; size_tier?: Array<string> | null; variation_count_from?: number | null; variation_count_to?: number | null; words_count_from?: number | null; words_count_to?: number | null; }; limit?: number; marketplace?: "US" | "CA" | "MX" | "DE" | "ES" | "IT" | "FR" | "UK" | "IN" | "NL" | "AU" | "JP" | "AE" | "BR" | "SA"; session_id?: string; sort_by?: "default" | "phrase" | "-phrase" | "last_30_days_sales" | "-last_30_days_sales" | "monthly_revenue" | "-monthly_revenue" | "price" | "-price" | "reviews_number" | "-reviews_number" | "reviews_rating" | "-reviews_rating" | "best_sellers_rank" | "-best_sellers_rank" | "sellers_number" | "-sellers_number" | "variation_count" | "-variation_count" | "exact_impression_count_last_30_days" | "-exact_impression_count_last_30_days" | "words_count" | "-words_count" | "results_number" | "-results_number" | "broad_reach_potential" | "-broad_reach_potential" | null; }): Promise<CallToolResult<{
  // Success payload; full schema in the top-level data property.
  data: { [key: string]: unknown; };
  // No error on success.
  error: null;
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
} | {
  // No data when the tool fails.
  data: null;
  // Error payload; full schema in the top-level error property.
  error: { [key: string]: unknown; };
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
}>>; };
```

## 105. `search_browse_nodes`

Helium 10 Browse Node Finder for Review Insights. Given a marketplace and a fuzzy Amazon category phrase (query), returns up to 5 candidate browse_node_id values, each paired with the official Amazon category name (displayName) and its root-to-leaf category path for disambiguating same-name categories. Use when the user names a category in natural language and does not yet have a confirmed browse node id—before calling get_category_review_analysis. Does not return Customer Feedback review or return analysis. Ask the user to confirm one browse_node_id, then call get_category_review_analysis. Not for: already-confirmed browse node ids (call get_category_review_analysis directly); ASIN-level review insights (use get_asin_review_analysis). Marketplace must be a two-letter uppercase code (e.g. US). Successful calls do not consume MCP quota. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_search_browse_nodes(args: {
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Amazon marketplace code. Two-letter UPPERCASE code; supported values: US, UK, FR, IT, DE, ES, JP. Lowercase or unsupported codes are rejected with INVALID_INPUT.INVALID_MARKETPLACE.
  marketplace: "US" | "UK" | "FR" | "IT" | "DE" | "ES" | "JP";
  // Fuzzy Amazon category phrase in natural language (e.g. "yoga mats"). Must be non-empty; blank or whitespace-only values are rejected with INVALID_INPUT.INVALID_KEYWORD.
  query: string;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{
  // Browse node candidates; null on hard failure. Zero hits is a success: candidates=[] with isError=false (helper calls never consume quota).
  data: {
  // Up to 5 candidate browse nodes, best match first.
  candidates: Array<{
  // Amazon browse node id. After the user confirms one candidate, pass it to get_category_review_analysis.
  browse_node_id: string;
  // Official Amazon category name.
  displayName: string;
  // Root-to-leaf category name path; use it to tell same-name leaf categories apart (e.g. Footwear > Men vs Sets > Men's).
  path: Array<string>;
}>;
  // Echo of the requested marketplace code.
  marketplace: string;
  // Echo of the requested category-name query.
  query: string;
} | null;
  // Set on failure; data is null and error.details carries request_id.
  error?: { code?: string; details?: { [key: string]: unknown; } | null; message?: string; remediation?: string | null; } | null;
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
}>>; };
```

## 106. `search_buybox_history`

Helium 10 Alerts — Buy Box ownership history for a specific ASIN over a date window. Returns offer-change events with Buy Box winner (seller ID), number of competing sellers, and timestamps, plus the seller's overall Buy Box win percentage for the queried period (computed over the whole window, not just the returned page). Use for analysing Buy Box competition patterns and win share over time; not for general listing-change monitoring (use search_alert_events). Returns up to `limit` rows per call (default 50, max 10000); one page inlines at most ~100 rows / ~1MB, and oversized pages are truncated and continue via cursor. If data.pagination.has_more is true the result is incomplete: call again with `cursor` = data.pagination.next_cursor, but prefer narrowing filters over paginating through everything. Cursors expire 30 minutes after being issued and are bound to the query that issued them. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_search_buybox_history(args: {
  // Amazon ASIN to query, 10-char alphanumeric (e.g. B01LR5S6HK).
  asin: string;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Opaque pagination cursor from a previous response's pagination.next_cursor. Omit for the first page. Pass it back verbatim with the same query arguments (limit may change between pages); never construct or edit it. Cursors are reusable but expire 30 minutes after being issued (pagination.cursor_expires_at) and are bound to the query that issued them.
  cursor?: string | null;
  // End of the query window (inclusive). Format: YYYY-MM-DD or YYYY-MM-DD HH:MM:SS (UTC). Example: "2025-03-31" or "2025-03-31 23:59:59". Optional, but must be provided together with start_time — providing only one of the two is invalid. Omit both to use the default 7-day window.
  end_time?: string | null;
  // Number of rows to return. Defaults to 50, max 10000; out-of-range values are clamped rather than rejected. request only what the user needs — up to 100 rows per page come back inline (slightly larger asks are truncated and continue via `cursor`). When pagination.has_more is true, fetch the next batch with `cursor`. Do NOT probe with limit=1 to learn the dataset size — every page carries the same total_count/has_more metadata, so ask for the rows you actually need and read the metadata of that response.
  limit?: number;
  // Marketplace code: US, UK, DE, JP, etc.
  marketplace: "US" | "CA" | "MX" | "DE" | "ES" | "IT" | "FR" | "UK" | "IN" | "NL" | "AU" | "JP" | "AE" | "BR" | "SA";
  // Helium 10 seller identifier.
  seller_id: string;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Start of the query window (inclusive). Format: YYYY-MM-DD or YYYY-MM-DD HH:MM:SS (UTC). Example: "2025-01-01" or "2025-01-01 00:00:00". Optional, but must be provided together with end_time — providing only one of the two is invalid. Omit both to use the default 7-day window.
  start_time?: string | null;
}): Promise<CallToolResult<{
  // Buy Box history payload. Null on error.
  data: {
  // Seller's Buy Box win percentage over the queried window (0.0–1.0, e.g. 0.65 = 65%). Null when the window has no offer-change data to compute a percentage from (rows=[]) — distinct from a real 0.0 (had data, won 0% of the time).
  buybox_win_percentage?: number | null;
  // Pagination metadata for this page of rows. When has_more is true the result set is NOT complete — pass next_cursor back to fetch the next batch.
  pagination?: {
  // Pagination consistency mode. 'live': every page queries live data (no snapshot); if the underlying data changes between pages, rows may shift across page boundaries - restart without `cursor` for a fresh view.
  consistency: string;
  // Rows delivered so far across this cursor chain, including this response (exported file rows count for download delivery).
  cumulative_count: number;
  // ISO-8601 UTC time when next_cursor stops being accepted; null when next_cursor is null. Expired cursors are rejected - restart from the first page by omitting `cursor`.
  cursor_expires_at: string | null;
  // True when more rows exist beyond this response.
  has_more: boolean;
  // Present only when the requested `limit` was adjusted: the value actually applied after clamping into [1, max].
  limit_applied?: number;
  // Present only when the requested `limit` was adjusted: the value as requested, before clamping.
  limit_requested?: number;
  // Opaque cursor for the next batch; pass it back verbatim as `cursor` with the same query arguments. Null when has_more is false. When truncation_reason is 'traversal_bound' this cursor is a terminal marker: passing it back returns a guidance error instead of rows - narrow the filters, and never restart from the first page to 'continue'.
  next_cursor: string | null;
  // Rows returned inline in this response (0 in download responses).
  returned_count: number;
  // Total rows matching the query, when known; null when the tail has not been reached yet (rely on has_more).
  total_count: number | null;
  // True when this response covers less than the requested window; see truncation_reason for why and how to continue.
  truncated: boolean;
  // Why truncated is true. 'size_limit': page cut to fit the inline response budget - continue with next_cursor. 'traversal_bound': this tool cannot paginate past its per-query row cap; next_cursor is a terminal marker (reusing it returns an error) - narrow the filters to reach the remaining rows, never restart from page 1. Null when truncated is false.
  truncation_reason: "size_limit" | "traversal_bound" | null;
};
  // One-sentence coverage note for this response (which rows are included and how to get the rest). Read it before concluding the data is complete.
  results_summary?: string;
  // Chronological list of Buy Box offer-change events for the ASIN.
  rows?: Array<{
  // Seller ID of the Buy Box holder at this event.
  buy_box_winner?: string | null;
  // Date of the offer-change event, format YYYY-MM-DD.
  event_date?: string | null;
  // Number of competing sellers at the moment of this offer change.
  number_of_sellers?: number | null;
  // Unix timestamp (seconds, UTC) of the offer change.
  time_of_offer_change?: number | null;
}>;
} | null;
  error: null;
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
} | {
  data: null;
  // Structured error response from io-api.
  error: {
  // MCP string error code.
  code: string;
  // Optional structured error details.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Optional human-readable remediation guidance (may be omitted).
  remediation?: string;
};
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
}>>; };
```

## 107. `search_competitors_by_asin`

Helium 10 Competitor Search (formerly Black Box — Competitors). Given one required seed ASIN, returns similar competing listings with price, sales, revenue, reviews, and overlap signals; Advanced filters run on the database server. Use for competitor benchmarks, pricing studies, and pre-launch competitive scans. Not for: broad product discovery without a seed ASIN (use search_products); keyword niche sets (use search_niche_by_keyword). If the user asks for historical Price, call get_asin_price_history; for BSR history, call get_asin_bsr_history; for Review Count / Rating history, call get_asin_reviews_history — once per selected ASIN. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_search_competitors_by_asin(args: { asin: string; context?: string; filters?: { age_from?: number | null; age_to?: number | null; bb_seller_id?: string | null; best_month?: string | null; brand_exclude?: string | null; brand_include?: string | null; category?: Array<string | number> | null; child_monthly_revenue_from?: number | null; child_monthly_revenue_to?: number | null; child_monthly_sales_from?: number | null; child_monthly_sales_to?: number | null; fulfillment?: Array<string> | null; monthly_revenue_from?: number | null; monthly_revenue_to?: number | null; monthly_sales_from?: number | null; monthly_sales_to?: number | null; number_of_images_from?: number | null; number_of_images_to?: number | null; number_of_sellers_from?: number | null; number_of_sellers_to?: number | null; price_change_from?: number | null; price_change_to?: number | null; price_from?: number | null; price_to?: number | null; review_count_from?: number | null; review_count_to?: number | null; review_rating_from?: number | null; review_rating_to?: number | null; review_velocity_from?: number | null; review_velocity_to?: number | null; sales_change_from?: number | null; sales_change_to?: number | null; sales_rank_from?: number | null; sales_rank_to?: number | null; sales_to_reviews_from?: number | null; sales_to_reviews_to?: number | null; sales_year_over_year_from?: number | null; sales_year_over_year_to?: number | null; seller_exclude?: string | null; seller_include?: string | null; size_tier?: Array<string> | null; title_exclude_keyword?: string | null; title_include_keyword?: string | null; variation_count_from?: number | null; variation_count_to?: number | null; weight_from?: number | null; weight_to?: number | null; }; marketplace?: "US" | "CA" | "MX" | "DE" | "ES" | "IT" | "FR" | "UK" | "IN" | "NL" | "AU" | "JP" | "AE" | "BR" | "SA"; session_id?: string; sort_by?: "default" | "monthly_sales" | "-monthly_sales" | "child_monthly_sales" | "-child_monthly_sales" | "monthly_revenue" | "-monthly_revenue" | "child_monthly_revenue" | "-child_monthly_revenue" | "price" | "-price" | "review_count" | "-review_count" | "sales_rank" | "-sales_rank" | "number_of_sellers" | "-number_of_sellers" | "sales_year_over_year" | "-sales_year_over_year" | "price_change" | "-price_change" | "sales_change" | "-sales_change" | "sales_to_reviews" | "-sales_to_reviews" | "number_of_images" | "-number_of_images" | "variation_count" | "-variation_count" | "weight" | "-weight" | "age" | "-age" | "p_index" | "-p_index" | null; }): Promise<CallToolResult<{
  // Success payload; full schema in the top-level data property.
  data: { [key: string]: unknown; };
  // No error on success.
  error: null;
} | {
  // No data when the tool fails.
  data: null;
  // Error payload; full schema in the top-level error property.
  error: { [key: string]: unknown; };
}>>; };
```

## 108. `search_competitors_by_item_id`

Helium 10 Competitor Search for Walmart. Given one Walmart item, returns the walmart.com items that rank on the same search phrases, ordered by how strongly they overlap, each with its item ID, title, and image. Identity fields only - no metrics. Use for: building a competitor set on Walmart when the user has an item but no competitor list, before a keyword comparison or a listing review. The item IDs it returns are meant to be fed into compare_item_keywords or get_keywords_by_item_id. Not for: Amazon products - use search_competitors_by_asin instead. Do not use it to compare competitors on price, sales, revenue, BSR, or reviews: Walmart has no such data here and this tool takes no filter or sort arguments. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_search_competitors_by_item_id(args: {
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Single Walmart item ID, all digits (e.g. 10450114). It is the numeric segment at the end of a walmart.com product URL. Amazon ASINs are not accepted here - use search_competitors_by_asin for Amazon.
  item_id: string;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{
  // Walmart competitor search payload when the call succeeds. May be omitted or empty on failure—check for an ``error`` payload.
  data: {
  // Walmart items that rank on the same search phrases as the given item, at most 20, in the order the upstream competitor query returns them. Identity fields only — no price, sales, rating or overlap metric. An item with no competitors returns an empty array, not an error.
  competitor_products: Array<{
  // Competitor main image. A generic placeholder URL when walmart.com has no image for it.
  image_url?: string;
  // Walmart item ID of the competing product. Feed it to get_keywords_by_item_id or compare_item_keywords.
  item_id?: string;
  // Competitor product title on walmart.com.
  title?: string;
}>;
};
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
} | {
  // Structured error response from io-api for MCP endpoints.
  error: {
  // MCP string error code.
  code: string;
  // Optional structured error details.
  details?: { [key: string]: unknown; };
  // Human-readable error message.
  message: string;
  // Optional human-readable remediation guidance (may be omitted).
  remediation?: string;
};
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
}>>; };
```

## 109. `search_inactive_products`

Helium 10 Product Research (formerly Black Box — Products, inactive view). Discovers Amazon listings that are now inactive/discontinued/dropped but were active and tracked within the last 360 days, by filters (category, last-active-date, historical sales/revenue, reviews, price, brand, fulfillment). Returns last known price, 360-day historical sales & revenue, review count, and rating. Use for market-gap mining, vacated-niche discovery, dropped-competitor post-mortems, and replication-candidate research. Returns up to `limit` rows per call (default 25, max 500); one page inlines at most ~500 rows / ~1MB, and oversized pages are truncated and continue via cursor. If data.pagination.has_more is true the result is incomplete: call again with `cursor` = data.pagination.next_cursor, but prefer narrowing filters over paginating through everything. Cursors expire 30 minutes after being issued and are bound to the query that issued them. Not for: active listings (use search_products); keyword research (use search_amazon_keywords). If the user asks for historical Price for returned inactive products, call get_asin_price_history once per selected ASIN. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_search_inactive_products(args: { context?: string; cursor?: string | null; fields?: Array<string> | null; filters?: { age_from?: number | null; age_to?: number | null; brand_exclude?: string | null; brand_include?: string | null; category?: Array<string | number> | null; fulfillment?: Array<string> | null; identifiers?: Array<string> | null; last_360_days_child_revenue_from?: number | null; last_360_days_child_revenue_to?: number | null; last_360_days_child_sales_from?: number | null; last_360_days_child_sales_to?: number | null; last_360_days_revenue_from?: number | null; last_360_days_revenue_to?: number | null; last_360_days_sales_from?: number | null; last_360_days_sales_to?: number | null; last_active_date_from?: string | null; last_active_date_to?: string | null; latest_price_from?: number | null; latest_price_to?: number | null; number_of_images_from?: number | null; number_of_images_to?: number | null; review_count_from?: number | null; review_count_to?: number | null; reviews_rating_from?: number | null; reviews_rating_to?: number | null; seller_country_exclude?: string | null; seller_country_include?: string | null; seller_exclude?: string | null; seller_include?: string | null; size_tier?: Array<string> | null; title_exclude_keyword?: string | null; title_include_keyword?: string | null; variation_count_from?: number | null; variation_count_to?: number | null; weight_from?: number | null; weight_to?: number | null; }; limit?: number; marketplace?: "US" | "CA" | "MX" | "DE" | "ES" | "IT" | "FR" | "UK" | "IN" | "NL" | "AU" | "JP" | "AE" | "BR" | "SA"; session_id?: string; sort_by?: "default" | "age" | "-age" | "buy_box_seller" | "-buy_box_seller" | "buy_box_seller_country" | "-buy_box_seller_country" | "best_month" | "-best_month" | "brand" | "-brand" | "review_count" | "-review_count" | "reviews_rating" | "-reviews_rating" | "sales_rank" | "-sales_rank" | "seller_type" | "-seller_type" | "size_tier" | "-size_tier" | "title" | "-title" | "weight" | "-weight" | "variation_count" | "-variation_count" | "number_of_images" | "-number_of_images" | "last_active_date" | "-last_active_date" | "latest_price" | "-latest_price" | "last_360_days_sales" | "-last_360_days_sales" | "last_360_days_revenue" | "-last_360_days_revenue" | "last_360_days_child_sales" | "-last_360_days_child_sales" | "last_360_days_child_revenue" | "-last_360_days_child_revenue" | null; }): Promise<CallToolResult<{
  // Success payload; full schema in the top-level data property.
  data: { [key: string]: unknown; };
  // No error on success.
  error: null;
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
} | {
  // No data when the tool fails.
  data: null;
  // Error payload; full schema in the top-level error property.
  error: { [key: string]: unknown; };
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
}>>; };
```

## 110. `search_niche_by_keyword`

Helium 10 Niche Search (formerly Black Box — Niche). Given one required search keyword, returns a niche-level summary (totals/averages) plus related products with Parent/ASIN sales, revenue, price, BSR, and reviews; Advanced filters run on the database server. Use for niche sizing and opportunity scans after the user has a search term. Not for: product discovery without a keyword (use search_products); keyword-row mining (use search_amazon_keywords); ASIN competitor sets (use search_competitors_by_asin). If the user asks for historical Price, call get_asin_price_history; for BSR history, call get_asin_bsr_history; for Review Count / Rating history, call get_asin_reviews_history — once per selected ASIN. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_search_niche_by_keyword(args: { context?: string; filters?: { age_from?: number | null; age_to?: number | null; bb_seller_id?: string | null; best_month?: string | null; brand_exclude?: string | null; brand_include?: string | null; category?: Array<string | number> | null; child_monthly_revenue_from?: number | null; child_monthly_revenue_to?: number | null; child_monthly_sales_from?: number | null; child_monthly_sales_to?: number | null; fulfillment?: Array<string> | null; monthly_revenue_from?: number | null; monthly_revenue_to?: number | null; monthly_sales_from?: number | null; monthly_sales_to?: number | null; number_of_images_from?: number | null; number_of_images_to?: number | null; number_of_sellers_from?: number | null; number_of_sellers_to?: number | null; price_change_from?: number | null; price_change_to?: number | null; price_from?: number | null; price_to?: number | null; review_count_from?: number | null; review_count_to?: number | null; review_rating_from?: number | null; review_rating_to?: number | null; review_velocity_from?: number | null; review_velocity_to?: number | null; sales_change_from?: number | null; sales_change_to?: number | null; sales_rank_from?: number | null; sales_rank_to?: number | null; sales_to_reviews_from?: number | null; sales_to_reviews_to?: number | null; sales_year_over_year_from?: number | null; sales_year_over_year_to?: number | null; seller_exclude?: string | null; seller_include?: string | null; size_tier?: Array<string> | null; title_exclude_keyword?: string | null; title_include_keyword?: string | null; variation_count_from?: number | null; variation_count_to?: number | null; weight_from?: number | null; weight_to?: number | null; }; marketplace?: "US" | "CA" | "MX" | "DE" | "ES" | "IT" | "FR" | "UK" | "IN" | "NL" | "AU" | "JP" | "AE" | "BR" | "SA"; phrase: string; session_id?: string; sort_by?: "default" | "monthly_sales" | "-monthly_sales" | "child_monthly_sales" | "-child_monthly_sales" | "monthly_revenue" | "-monthly_revenue" | "child_monthly_revenue" | "-child_monthly_revenue" | "price" | "-price" | "review_count" | "-review_count" | "sales_rank" | "-sales_rank" | "number_of_sellers" | "-number_of_sellers" | "sales_year_over_year" | "-sales_year_over_year" | "price_change" | "-price_change" | "sales_change" | "-sales_change" | "sales_to_reviews" | "-sales_to_reviews" | "number_of_images" | "-number_of_images" | "variation_count" | "-variation_count" | "weight" | "-weight" | "age" | "-age" | "organic_rank" | "-organic_rank" | null; }): Promise<CallToolResult<{
  // Success payload; full schema in the top-level data property.
  data: { [key: string]: unknown; };
  // No error on success.
  error: null;
} | {
  // No data when the tool fails.
  data: null;
  // Error payload; full schema in the top-level error property.
  error: { [key: string]: unknown; };
}>>; };
```

## 111. `search_product_targets_by_asin`

Helium 10 Product Targeting (formerly Black Box — Product Targeting). Given 1–10 seed ASINs (asin_list), returns target ASINs from Frequently Bought Together, Customers Also Bought, and Amazon Suggested for PPC product targeting and Sponsored Display. At most 10 ASINs per call; more than 10 must error (do not silently truncate). Use to plan ad placements, bundles, and complementary catalog moves. Not for: direct competitor similarity (use search_competitors_by_asin). If the user asks for historical Price, call get_asin_price_history; for BSR history, call get_asin_bsr_history; for Review Count / Rating history, call get_asin_reviews_history — once per selected ASIN. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_search_product_targets_by_asin(args: { asin_list: Array<string>; context?: string; filters?: { age_from?: number | null; age_to?: number | null; bb_seller_id?: string | null; best_month?: string | null; brand_exclude?: string | null; brand_include?: string | null; category?: Array<string | number> | null; child_monthly_revenue_from?: number | null; child_monthly_revenue_to?: number | null; child_monthly_sales_from?: number | null; child_monthly_sales_to?: number | null; fulfillment?: Array<string> | null; monthly_revenue_from?: number | null; monthly_revenue_to?: number | null; monthly_sales_from?: number | null; monthly_sales_to?: number | null; number_of_images_from?: number | null; number_of_images_to?: number | null; number_of_sellers_from?: number | null; number_of_sellers_to?: number | null; price_change_from?: number | null; price_change_to?: number | null; price_from?: number | null; price_to?: number | null; review_count_from?: number | null; review_count_to?: number | null; review_rating_from?: number | null; review_rating_to?: number | null; review_velocity_from?: number | null; review_velocity_to?: number | null; sales_change_from?: number | null; sales_change_to?: number | null; sales_rank_from?: number | null; sales_rank_to?: number | null; sales_to_reviews_from?: number | null; sales_to_reviews_to?: number | null; sales_year_over_year_from?: number | null; sales_year_over_year_to?: number | null; seller_exclude?: string | null; seller_include?: string | null; size_tier?: Array<string> | null; title_exclude_keyword?: string | null; title_include_keyword?: string | null; variation_count_from?: number | null; variation_count_to?: number | null; weight_from?: number | null; weight_to?: number | null; }; marketplace?: "US" | "CA" | "MX" | "DE" | "ES" | "IT" | "FR" | "UK" | "IN" | "NL" | "AU" | "JP" | "AE" | "BR" | "SA"; session_id?: string; sort_by?: "default" | "monthly_sales" | "-monthly_sales" | "child_monthly_sales" | "-child_monthly_sales" | "monthly_revenue" | "-monthly_revenue" | "child_monthly_revenue" | "-child_monthly_revenue" | "price" | "-price" | "review_count" | "-review_count" | "sales_rank" | "-sales_rank" | "number_of_sellers" | "-number_of_sellers" | "sales_year_over_year" | "-sales_year_over_year" | "price_change" | "-price_change" | "sales_change" | "-sales_change" | "sales_to_reviews" | "-sales_to_reviews" | "number_of_images" | "-number_of_images" | "variation_count" | "-variation_count" | "weight" | "-weight" | "age" | "-age" | null; source?: Array<"F" | "A" | "C"> | null; weeks?: number | null; }): Promise<CallToolResult<{
  // Success payload; full schema in the top-level data property.
  data: { [key: string]: unknown; };
  // No error on success.
  error: null;
} | {
  // No data when the tool fails.
  data: null;
  // Error payload; full schema in the top-level error property.
  error: { [key: string]: unknown; };
}>>; };
```

## 112. `search_products`

Helium 10 Product Research (formerly Black Box — Products, active view). Discovers currently-active Amazon listings by product filters (category, brand/seller, price, sales, revenue, BSR, reviews, rating, sellers, fulfillment, size tier, opportunity score, identifiers, and more). Returns a ranked list with revenue estimates, sales velocity, review metrics, and opportunity scores. Use for product opportunity discovery, niche sizing, competitor/brand/seller analysis, category supply research, and sourcing qualification. Returns up to `limit` rows per call (default 25, max 500); one page inlines at most ~500 rows / ~1MB, and oversized pages are truncated and continue via cursor. If data.pagination.has_more is true the result is incomplete: call again with `cursor` = data.pagination.next_cursor, but prefer narrowing filters over paginating through everything. Cursors expire 30 minutes after being issued and are bound to the query that issued them. Not for: discontinued listings (use search_inactive_products); keyword-first research (use search_amazon_keywords); ABA Top Search Terms (use search_amazon_brand_analytics). If the user asks for historical Price, call get_asin_price_history; for BSR history, call get_asin_bsr_history; for Review Count / Rating history, call get_asin_reviews_history — once per selected ASIN. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_search_products(args: { context?: string; cursor?: string | null; fields?: Array<string> | null; filters?: { age_from?: number | null; age_to?: number | null; authors_exclude?: string | null; authors_include?: string | null; best_month?: string | null; book_type?: string | null; brand_exclude?: string | null; brand_include?: string | null; category?: Array<string | number> | null; child_monthly_revenue_from?: number | null; child_monthly_revenue_to?: number | null; child_monthly_sales_from?: number | null; child_monthly_sales_to?: number | null; child_sales_change_from?: number | null; child_sales_change_to?: number | null; child_sales_year_over_year_from?: number | null; child_sales_year_over_year_to?: number | null; child_year_sales_from?: number | null; child_year_sales_to?: number | null; fulfillment?: Array<string> | null; has_returned_tag?: boolean | null; has_series?: boolean | null; identifiers?: Array<string> | null; influencer_videos_from?: number | null; influencer_videos_to?: number | null; language?: Array<string> | null; length_unit?: string | null; length_value_from?: number | null; length_value_to?: number | null; monthly_revenue_from?: number | null; monthly_revenue_to?: number | null; monthly_sales_from?: number | null; monthly_sales_to?: number | null; number_of_images_from?: number | null; number_of_images_to?: number | null; number_of_lower_influencers_videos_from?: number | null; number_of_lower_influencers_videos_to?: number | null; number_of_sellers_from?: number | null; number_of_sellers_to?: number | null; number_of_upper_influencers_videos_from?: number | null; number_of_upper_influencers_videos_to?: number | null; number_of_videos_from?: number | null; number_of_videos_to?: number | null; offsite_commission_from?: number | null; offsite_commission_rate_from?: number | null; offsite_commission_rate_to?: number | null; offsite_commission_to?: number | null; onsite_commission_from?: number | null; onsite_commission_rate_from?: number | null; onsite_commission_rate_to?: number | null; onsite_commission_to?: number | null; opportunity_score_from?: number | null; opportunity_score_to?: number | null; price_change_from?: number | null; price_change_to?: number | null; price_from?: number | null; price_to?: number | null; publication_date_from?: number | null; publication_date_to?: number | null; publisher_exclude?: string | null; publisher_include?: string | null; review_count_from?: number | null; review_count_to?: number | null; review_velocity_from?: number | null; review_velocity_to?: number | null; reviews_rating_from?: number | null; reviews_rating_to?: number | null; sales_change_from?: number | null; sales_change_to?: number | null; sales_rank_from?: number | null; sales_rank_to?: number | null; sales_to_reviews_from?: number | null; sales_to_reviews_to?: number | null; sales_year_over_year_from?: number | null; sales_year_over_year_to?: number | null; seller_country_exclude?: string | null; seller_country_include?: string | null; seller_exclude?: string | null; seller_include?: string | null; size_tier?: Array<string> | null; title_exclude_keyword?: string | null; title_include_keyword?: string | null; variation_count_from?: number | null; variation_count_to?: number | null; weight_from?: number | null; weight_to?: number | null; year_sales_from?: number | null; year_sales_to?: number | null; }; limit?: number; marketplace?: "US" | "CA" | "MX" | "DE" | "ES" | "IT" | "FR" | "UK" | "IN" | "NL" | "AU" | "JP" | "AE" | "BR" | "SA"; session_id?: string; sort_by?: "default" | "price" | "monthly_sales" | "monthly_revenue" | "sales_rank" | "review_count" | "sales_to_reviews" | "variation_count" | "buy_box_seller" | "year_sales" | "child_monthly_revenue" | "child_monthly_sales" | "child_sales_year_over_year" | "child_sales_change" | "child_year_sales" | "buy_box_seller_country" | "number_of_sellers" | "sales_year_over_year" | "price_change" | "sales_change" | "number_of_images" | "weight" | "age" | "number_of_videos" | "-price" | "-monthly_sales" | "-monthly_revenue" | "-sales_rank" | "-review_count" | "-sales_to_reviews" | "-variation_count" | "-buy_box_seller" | "-year_sales" | "-child_monthly_revenue" | "-child_monthly_sales" | "-child_sales_year_over_year" | "-child_sales_change" | "-child_year_sales" | "-buy_box_seller_country" | "-number_of_sellers" | "-sales_year_over_year" | "-price_change" | "-sales_change" | "-number_of_images" | "-weight" | "-age" | "-number_of_videos" | "authors" | "published_at" | "published_month" | "language" | "publisher" | "book_type" | "-authors" | "-published_at" | "-published_month" | "-language" | "-publisher" | "-book_type" | null; }): Promise<CallToolResult<{
  // Success payload; full schema in the top-level data property.
  data: { [key: string]: unknown; };
  // No error on success.
  error: null;
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
} | {
  // No data when the tool fails.
  data: null;
  // Error payload; full schema in the top-level error property.
  error: { [key: string]: unknown; };
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
}>>; };
```

## 113. `search_tiktok_hot_videos`

Helium 10 TikTok Hot Videos. Searches trending TikTok Shop videos by keyword, category, and time window, returning each video plus the product it promotes, the creator who posted it, and engagement/sales metrics. This is a TikTok Shop tool; not for Amazon research (use the Amazon MCP tools) and not for product/influencer search directly (use search_tiktok_products / search_tiktok_influencers). `limit` accepts 10-100 rows per call (default 10). At most 500 rows are reachable in total for one query; beyond that the result is truncated (truncation_reason='traversal_bound'). If data.pagination.has_more is true the result is incomplete: call again with `cursor` = data.pagination.next_cursor, but prefer narrowing filters over paginating through everything. Cursors expire 30 minutes after being issued and are bound to the query that issued them. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_search_tiktok_hot_videos(args: {
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // Opaque pagination cursor from a previous response's pagination.next_cursor. Omit for the first page. Pass it back verbatim with the same query arguments (limit may change between pages); never construct or edit it. Cursors are reusable but expire 30 minutes after being issued (pagination.cursor_expires_at) and are bound to the query that issued them.
  cursor?: string | null;
  // Structured trending TikTok Shop video search filters object. Pass an object with the documented fields; do not pass a string.
  filters?: {
  // Product category as natural-language text (for example "beauty") or a known category id. Resolved internally against TikTok's category tree; no separate lookup call is needed. Text that cannot be matched (directly, via LLM, or via fuzzy match) raises an INVALID_INPUT.RESOURCE_NOT_FOUND error instead of silently dropping the filter — retry with different wording or omit the field. Filters on the featured product's category. One or more categories may be given; a video matching any of them is included (OR).
  category?: Array<string> | null;
  // Time window in days used to select and rank trending videos.
  last_few_days?: 7 | 14 | 30;
  // Free-text search matching the video caption, hashtags, or featured product title.
  search?: string | null;
  // Sort ascending instead of the default descending order.
  sort_asc?: boolean;
  // Field to sort the results by.
  sort_by?: "itemsSold" | "gmv" | "views" | "likes" | "shares" | "engagementRate" | "publishTime" | null;
};
  // Number of rows to return, 10-100 (default 10); out-of-range values are rejected. At most 500 rows are reachable in total for one query. When pagination.has_more is true, fetch the next batch with `cursor`. Do NOT probe with a small limit to learn the dataset size — every page carries the same total_count/has_more metadata, so ask for the rows you actually need and read the metadata of that response.
  limit?: number;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
}): Promise<CallToolResult<{
  // Success payload; full schema in the top-level data property.
  data: { [key: string]: unknown; };
  // No error on success.
  error: null;
} | {
  // No data when the tool fails.
  data: null;
  // Error payload; full schema in the top-level error property.
  error: { [key: string]: unknown; };
}>>; };
```

## 114. `search_tiktok_influencers`

Helium 10 TikTok Influencer Finder. Searches TikTok creators by keyword, category, follower count, age/gender, video/live view and engagement rates, GMV band, units-sold band, and commission-rate band. Just US marketplace. category accepts natural-language text (e.g. "beauty") or a known category_id; it is resolved internally against TikTok's category tree, no separate lookup call needed. Not for: product search (use search_tiktok_products); Amazon-side research (use the Amazon MCP tools). `limit` accepts 10-100 rows per call (default 10). At most 1000 rows are reachable in total for one query; beyond that the result is truncated (truncation_reason='traversal_bound'). If data.pagination.has_more is true the result is incomplete: call again with `cursor` = data.pagination.next_cursor, but prefer narrowing filters over paginating through everything. Cursors expire 30 minutes after being issued and are bound to the query that issued them. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_search_tiktok_influencers(args: { context?: string; cursor?: string | null; filters?: { age_ranges?: Array<"18-24" | "25-34" | "35-44" | "45-54" | "55+"> | null; avg_commission_rate_range?: "<5%" | "<10%" | "<15%" | "<20%" | null; avg_ec_live_view_count_max?: number | null; avg_ec_live_view_count_min?: number | null; avg_ec_video_play_count_max?: number | null; avg_ec_video_play_count_min?: number | null; avg_gmv_per_buyer_amount_max?: number | null; avg_gmv_per_buyer_amount_min?: number | null; category?: string | null; ec_live_engagement_rate_max?: number | null; ec_live_engagement_rate_min?: number | null; ec_video_engagement_rate_max?: number | null; ec_video_engagement_rate_min?: number | null; follower_count_max?: number | null; follower_count_min?: number | null; gender?: "FEMALE" | "MALE" | null; gmv_amount_ranges?: Array<"0-100" | "100-1K" | "1K-10K" | "10K+"> | null; keywords?: string | null; promoted_product_num_max?: number | null; promoted_product_num_min?: number | null; sort_asc?: boolean; sort_by?: "followerCount" | "avgEcVideoViewCount" | "ecVideoEngagementRate" | "avgEcLiveViewCount" | "ecLiveEngagementRate" | "avgCommissionRate" | "gmvAmount" | "unitsSold" | "promotedProductNum" | "avgGmvPerBuyerAmount" | "avgEcVideoPlayCount" | null; units_sold_ranges?: Array<"0-10" | "10-100" | "100-1K" | "1K+"> | null; }; limit?: number; session_id?: string; }): Promise<CallToolResult<{
  // Success payload; full schema in the top-level data property.
  data: { [key: string]: unknown; };
  // No error on success.
  error: null;
} | {
  // No data when the tool fails.
  data: null;
  // Error payload; full schema in the top-level error property.
  error: { [key: string]: unknown; };
}>>; };
```

## 115. `search_tiktok_products`

Helium 10 TikTok Product Finder. Searches TikTok Shop products by keyword, brand, shop, category, price, rating, shipping, listing recency, units sold, GMV, GMV trend, commission rate, and affiliate/video engagement ranges. This is a TikTok Shop tool, not Amazon — for Amazon product research use search_products (Black Box) instead; the two cover different platforms and are not interchangeable. category accepts natural-language text (e.g. "beauty") or a known category_id; it is resolved internally against TikTok's category tree, no separate lookup call needed. market is currently US only. `limit` accepts 10-100 rows per call (default 10). At most 500 rows are reachable in total for one query; beyond that the result is truncated (truncation_reason='traversal_bound'). If data.pagination.has_more is true the result is incomplete: call again with `cursor` = data.pagination.next_cursor, but prefer narrowing filters over paginating through everything. Cursors expire 30 minutes after being issued and are bound to the query that issued them. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_search_tiktok_products(args: { context?: string; cursor?: string | null; filters?: { active_influencers_max?: number | null; active_influencers_min?: number | null; active_videos_max?: number | null; active_videos_min?: number | null; brand?: string | null; category?: string | null; commission_rate_max?: number | null; commission_rate_min?: number | null; free_shipping?: boolean | null; gmv_max?: number | null; gmv_min?: number | null; item_share_by_affiliate_max?: number | null; item_share_by_affiliate_min?: number | null; item_trend?: Array<"EXPLODING" | "GROWING" | "STABLE"> | null; items_sold_max?: number | null; items_sold_min?: number | null; last_few_days?: 7 | 14 | 30; listed_within_days?: 7 | 14 | 30 | null; market?: "US"; new_influencers_max?: number | null; new_influencers_min?: number | null; new_videos_max?: number | null; new_videos_min?: number | null; price_max?: number | null; price_min?: number | null; rating_max?: number | null; rating_min?: number | null; search_word?: string | null; shop_name?: string | null; sort_asc?: boolean; sort_by?: "commissionRate" | "itemsSold" | "gmv" | "gmvTrend" | "totalGmv" | "gmvShareByAffiliate" | "newInfluencers" | "newVideos" | "activeInfluencers" | "activeVideos" | "listedDate" | null; }; limit?: number; session_id?: string; }): Promise<CallToolResult<{
  // Success payload; full schema in the top-level data property.
  data: { [key: string]: unknown; };
  // No error on success.
  error: null;
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
} | {
  // No data when the tool fails.
  data: null;
  // Error payload; full schema in the top-level error property.
  error: { [key: string]: unknown; };
  // Account-level notice attached to an otherwise successful call (e.g. MCP call usage approaching the cycle limit). Absent when there is nothing to report — it is NOT part of the data you asked for. When present, relay `message` to the user verbatim, including any URL, on top of answering their actual question.
  notice?: {
  // Machine-readable notice code, e.g. USAGE.QUOTA_NEAR_LIMIT.
  code: string;
  // User-facing English text; relay it verbatim, do not paraphrase.
  message: string;
  // 'warning' means the user should act soon.
  severity: "info" | "warning";
};
}>>; };
```

## 116. `sync_listing_to_amazon`

Helium 10 Listing Builder — DESTRUCTIVE: pushes listing content (title / item_highlight / bullets / description) to the user's Amazon Seller Central via SP-API, then verifies it landed — the outcome is reported in `sync_status`. The only Listing Builder tool that mutates the seller's live data; it requires the user's explicit consent to publish, and merely reviewing or approving the text is NOT consent. No outcome is safe to auto-retry: resubmitting a payload Amazon already accepted writes twice. At least one of title / item_highlight / bullets / description must be present, or the call is rejected. This tool is part of plugin `Helium 10`.

Access your Helium 10 data

exec tool declaration:
```ts
declare const tools: { mcp__codex_apps__helium_10_sync_listing_to_amazon(args: {
  // Amazon ASIN (10-char alphanumeric, case-insensitive). DEFAULTS to the ASIN established in the current session (typically passed to `retrieve_listing_by_asin`). Do not proactively re-ask the user for this value — pass the session ASIN as the default. If the user explicitly says they want to sync this generated listing to a different ASIN, accept the override; the server verifies that whichever ASIN is provided is owned by the caller's linked seller accounts and that it matches the SKU.
  asin: string;
  // New bullet points (max 10 entries) — replaces the ENTIRE field: Amazon treats bullet points as ONE field, so a shorter array DELETES the live bullets beyond its length (send 2 onto a 5-bullet listing and bullets 3-5 are gone). Include every bullet in order, not just the changed one. Omit to leave bullets unchanged.
  bullets?: Array<string> | null;
  // In one short sentence, explain why you are calling this tool and what you are trying to accomplish for the user. Used for analytics only; it does not affect the tool's behavior.
  context?: string;
  // New product description. Omit to leave description unchanged.
  description?: string | null;
  // New Amazon Item Highlight text — a matched pair with `title`: pass it together with `title` whenever the title is being synced (both go together). Omit to leave it unchanged.
  item_highlight?: string | null;
  // REQUIRED. Marketplace short code (case-sensitive). Confirm the marketplace with the user once, before the first Listing Builder call of the session; every later Listing Builder tool reuses that same value — do NOT ask the user for it again once it is set. When a request_id is supplied, the server rejects any mismatch with that generation's marketplace. Supported values: US, CA, MX, DE, ES, IT, FR, UK, IN, NL, AU, JP, BE, BR.
  marketplace: "US" | "CA" | "MX" | "DE" | "ES" | "IT" | "FR" | "UK" | "IN" | "NL" | "AU" | "JP" | "BE" | "BR";
  // Optional. The request_id returned by `generate_listing` or `rewrite_listing_section` (same caller / same marketplace). When provided, the sync is validated against that prior generation. Omit to sync the provided content directly.
  request_id?: number | null;
  // REQUIRED on every call after your first. This MCP server associates all of your tool calls for a given task using session_id; a call that arrives without it cannot be associated with your earlier work and is treated as the start of an unrelated task, disconnected from the original goal. Omit it on your first call only — the server will issue one in a trailing [MCP INSTRUCTIONS] text block of the result — then echo that exact value on every later call. Never invent a value, and do not issue parallel tool calls until the server has issued your session_id. One session_id covers the whole goal from start to finish: when you spawn subagents you MUST give them this same session_id, or their work is severed from yours. session_id is internal plumbing: never show, quote or mention it to the user. Without session_id, this server does not function as intended.
  session_id?: string;
  // Seller SKU that maps to the ASIN in the seller's catalog.
  sku: string;
  // New product title. Omit to leave the existing title unchanged. `title` and `item_highlight` are a matched pair: when the user asks to sync the title, pass BOTH `title` and `item_highlight` together (the Item Highlight is generated alongside the title) — do NOT send `title` alone.
  title?: string | null;
}): Promise<CallToolResult<{
  // Successful Amazon Seller Central sync result. A non-ACCEPTED Amazon status surfaces as an MCP error (H10.LB_SP_API_REJECTED) rather than this success envelope, so status is effectively always 'ACCEPTED' here.
  data: {
  // Issues attached by Amazon to the submission (warnings / info). ERROR-severity issues come back via the MCP error channel instead.
  issues?: Array<{
  // Listing attribute names this issue refers to (may be empty / null).
  attribute_names?: Array<string | null> | null;
  // Amazon SP-API issue code (e.g. '90001').
  code?: string | null;
  // Human-readable issue description returned by Amazon.
  message?: string | null;
  // Issue severity: 'ERROR' / 'WARNING' / 'INFO'.
  severity?: string | null;
} | null> | null;
  // Source generate_listing / rewrite_listing_section record id.
  request_id?: number | null;
  // Amazon submission status (usually 'ACCEPTED').
  status?: string | null;
  // Amazon SP-API submission identifier for this patch.
  submission_id?: string | null;
  // Post-submission confirmation state: 'CONFIRMED' (read-back matched) or 'ACCEPTED_UNCONFIRMED' (Amazon accepted it but the read-back could not confirm — may still be propagating). ACCEPTED_UNCONFIRMED is NOT a failure; do NOT resubmit.
  sync_status?: string | null;
  // Subset of ['title', 'item_highlight', 'bullets', 'description'] actually submitted to Amazon.
  synced_fields?: Array<string | null> | null;
  // Non-fatal advisory, present only when sync_status == 'ACCEPTED_UNCONFIRMED'. Tells the user to verify manually in Seller Central and not resubmit.
  warning?: {
  // Warning code, e.g. 'SYNC_NOT_CONFIRMED'.
  code?: string | null;
  // Human-readable explanation.
  message?: string | null;
  // Suggested next step for the caller.
  remediation?: string | null;
} | null;
};
  // No error on success.
  error: null;
} | {
  // No data when the tool fails.
  data: null;
  // Tool execution error payload. Present when isError is true.
  error: {
  // Stable MCP tool error code.
  code: string;
  // Structured diagnostic details safe for the agent to inspect.
  details: { [key: string]: unknown; } | null;
  // Concise human-readable error message.
  message: string;
  // Actionable guidance the agent can use to correct and retry.
  remediation: string | null;
};
}>>; };
```

