# InputJsonProd.json — Complete Structure Documentation

## Top-Level Structure

```
InputJsonProd.json
├── id: "1605949056508614848"                    // Page ID
│
├── sections: [...]                              // Array of page sections
│   └── Section
│       ├── id: "23123"
│       ├── name: "Section One"
│       └── widgets: [...]                       // 41 widgets in this page
│
├── baseComponents: [...]                        // 68 shared/reusable component definitions
├── rules: [...]                                 // 149 conditional rules (style/data/visibility)
├── analytics: { schema: {...} }                 // Analytics schema references
├── pageConfigs: { "default": {...}, ... }       // Page-level styling
└── sectionConfigs: { "default": {...}, ... }    // Section-level styling
```

---

## All 41 Widgets in the Page

| # | Widget Name | ID |
|---|-------------|----|
| 0 | Single asset | 1587360743173931648 |
| 1 | Multiple assets | 1558310044843049472 |
| 2 | Call AI Dashboard - Upcoming meetings | 1733131036034816960 |
| 3 | Call AI Dashboard - Shared recordings | 1733129271675673536 |
| 4 | Call AI Dashboard - My recordings | 1733091020583689152 |
| 5 | Call AI Dashboard - Bookmarked recordings | 1733129894110700224 |
| 6 | Top-rated assets | 1407820184547058368 |
| 7 | Recently added assets | 1407818040360133312 |
| 8 | Bookmarked assets | 1425744880043746560 |
| 9 | Plays | 1571724892738390912 |
| 10 | Updated Assets You Might Be Interested In | 1407819378129199808 |
| 11 | Featured hubs | 1543824399853106624 |
| 12 | Offline Hubs | 1594640826448956608 |
| 13 | Trending assets | 1450017035190719040 |
| 14 | Offline assets | 1525022144651885056 |
| 15 | All hubs | 1407301409049502400 |
| 16 | All hubs | 1452860558491842560 |
| 17 | Recently viewed assets | 1407815474935063232 |
| 18 | Top-rated modules | 1572176270547791040 |
| 19 | Top-rated series | 1443467950996502016 |
| 20 | Take action | 1421000667368152896 |
| 21 | Custom HTML/CSS | 1537739165289580096 |
| 22 | Text | 1540224790978677760 |
| 23 | Image | 1547898105743515328 |
| 24 | Video | 1559770750709419520 |
| 25 | Spotlight | 1342062044811890048 |
| 26 | Featured Themes | 1293085622229523264 |
| 27 | Continue | 1291019986543562944 |
| 28 | Urgent | 1300800646410515776 |
| 29 | Required | 1300804562552087168 |
| 30 | What's new in assigned | 1300819130301014656 |
| 31 | Trending | 1300809963683177088 |
| 32 | What's new in public | 1300841043723281024 |
| 33 | Explore by series | 1293602571837496128 |
| 34 | Featured Themes | 1361581094840300544 |
| 35 | Call AI - My recordings | 1359060806281161792 |
| 36 | Featured resources | 1370297544308771904 |
| 37 | View All Assigned | 1301723191690885184 |
| 38 | Recently added assets see all | 1666864719502723840 |
| 39 | Showcased | 1635561039075664704 |
| 40 | All hubs - See All | 1878716175606143424 |

---

## Deep-Dive: Single Widget ("Single Asset" — Complex Example)

A widget has **8 main sections**:

```
Widget
├── 1. Metadata        (id, name, description, learnerName, localeUrl)
├── 2. mobileMapper    (API response path → local key mapping)
├── 3. ruleMapper      (conditional logic to derive display keys)
├── 4. data            (API request definition — method, host, headers, body)
├── 5. layout          (UI component tree — the actual visual structure)
├── 6. externals       (external key-value pairs)
├── 7. config          (widget behavior configuration)
└── 8. environment     (runtime variables for widget/layout/request)
```

---

### 1. Widget Metadata

```jsonc
{
  "id": "1587360743173931648",
  "name": "Single asset",
  "description": "Add an asset with title, description, engagement stats etc.",
  "learnerName": "Single Asset",
  "learnerDescription": "Add single asset by selecting them from a list or by attributes.",
  "localeUrl": "https://assets.mindtickle.com/mt-translations/prod/projects/1587360743173931648/build-.../translations/"
}
```

---

### 2. mobileMapper — Data Extraction Layer

Maps GraphQL API response paths to local keys used by the layout and rules.

```jsonc
"mobileMapper": [
  // === API response path mappers ===
  { "mapTo": "assetName",              "mapPath": "data.widgetLibrary.loadSingleAssetWidget.asset.name",                    "isOptional": false },
  { "mapTo": "assetDescription",       "mapPath": "data.widgetLibrary.loadSingleAssetWidget.asset.description",             "isOptional": false },
  { "mapTo": "showAssetDescription",   "mapPath": "data.widgetLibrary.loadSingleAssetWidget.showAssetDescription",          "isOptional": false },
  { "mapTo": "showAssetEngagementStats","mapPath": "data.widgetLibrary.loadSingleAssetWidget.showAssetEngagementStats",     "isOptional": false },
  { "mapTo": "showLastModifiedInfo",   "mapPath": "data.widgetLibrary.loadSingleAssetWidget.showLastModifiedInfo",          "isOptional": false },
  { "mapTo": "showAssetOwner",         "mapPath": "data.widgetLibrary.loadSingleAssetWidget.showAssetOwner",                "isOptional": false },
  { "mapTo": "categoriesThumbnail",    "mapPath": "data.widgetLibrary.loadSingleAssetWidget.asset.thumb.url",               "isOptional": false },
  { "mapTo": "viewCount",             "mapPath": "data.widgetLibrary.loadSingleAssetWidget.asset.stats.totalViews",         "isOptional": false },
  { "mapTo": "shareCount",            "mapPath": "data.widgetLibrary.loadSingleAssetWidget.asset.stats.shares",             "isOptional": false },
  { "mapTo": "rating",                "mapPath": "data.widgetLibrary.loadSingleAssetWidget.asset.stats.rating",             "isOptional": false },
  { "mapTo": "updatedOn",             "mapPath": "data.widgetLibrary.loadSingleAssetWidget.asset.updatedOn",                "isOptional": false },
  { "mapTo": "assetOwnerName",        "mapPath": "data.widgetLibrary.loadSingleAssetWidget.asset.assetOwner.user.displayName","isOptional": false },
  { "mapTo": "fileIconType",          "mapPath": "data.widgetLibrary.loadSingleAssetWidget.asset.fileType",                 "isOptional": false },
  { "mapTo": "assetIcon",             "mapPath": "data.widgetLibrary.loadSingleAssetWidget.asset.file.media.mediaType",     "isOptional": false },
  { "mapTo": "WidgetContent",         "mapPath": "data.widgetLibrary.loadSingleAssetWidget.asset.id",                       "isOptional": false },
  { "mapTo": "content",               "mapPath": "data.widgetLibrary.loadSingleAssetWidget.content",                        "isOptional": false },
  { "mapTo": "thumbnailPosition",     "mapPath": "data.widgetLibrary.loadSingleAssetWidget.thumbnailPosition",              "isOptional": false },
  { "mapTo": "assetAlignment",        "mapPath": "data.widgetLibrary.loadSingleAssetWidget.assetAlignment",                 "isOptional": false },

  // === Static value mappers (literal strings, not API paths) ===
  { "mapTo": "errorButtonText",        "mapPath": "Retry",                                                   "isOptional": false },
  { "mapTo": "retry",                  "mapPath": "retry://",                                                "isOptional": false },
  { "mapTo": "errorModuleText",        "mapPath": "Unable to load bookmarked recordings",                    "isOptional": false },
  { "mapTo": "errorModuleSubText",     "mapPath": "We are working hard to fix it. Please try again after some time.", "isOptional": false },
  { "mapTo": "showMoreButtonText",     "mapPath": "Show more",                                               "isOptional": false },
  { "mapTo": "noDescription",          "mapPath": "No description",                                          "isOptional": false },
  { "mapTo": "noAssetOwner",           "mapPath": "No asset owner",                                          "isOptional": false },

  // === Static URL mappers (image assets) ===
  { "mapTo": "viewsThumbnail",         "mapPath": "https://i.postimg.cc/HnLD2Qyh/pre-View2021.png",         "isOptional": true },
  { "mapTo": "sharesThumbnail",        "mapPath": "https://i.postimg.cc/qRwHymzK/share.png",                "isOptional": true },
  { "mapTo": "ratingsThumbnail",       "mapPath": "https://i.postimg.cc/x8FrFcGT/star-Filled.png",          "isOptional": true },

  // === Mobile action & event tracking ===
  { "mapTo": "mobileAction",           "mapPath": "data.widgetLibrary.loadSingleAssetWidget.asset.mobileUrl","isOptional": false },
  { "mapTo": "mobileEventName",        "mapPath": "single_asset_clicked",                                   "isOptional": false },
  { "mapTo": "mobileEventId",          "mapPath": "data.widgetLibrary.loadSingleAssetWidget.asset.id",      "isOptional": false },
  { "mapTo": "mobileEventTitle",       "mapPath": "data.widgetLibrary.loadSingleAssetWidget.asset.name",    "isOptional": false },

  // === Hide widget logic ===
  { "mapTo": "hideWidgetData",         "mapPath": "data.widgetLibrary.loadSingleAssetWidget.isAssetAvailable","isOptional": true },
  { "mapTo": "hideWidget",             "mapPath": "true",                                                    "isOptional": true },
  { "mapTo": "hideWidgetValue",        "mapPath": "true",                                                    "isOptional": true }
]
```

---

### 3. ruleMapper — Conditional Logic Layer

Rules apply transformations and branching logic to produce derived display keys.

#### Simple Rule (AND conditions)

```jsonc
{
  "mapTo": "w4c6k2",
  "conditionGroup": [{
    "condition": [
      { "id": "assetMediaIconRule" },
      { "id": "assetMediaFileIconRule" },
      { "id": "assetMediaColorRule" },
      { "id": "assetFileColorRule" }
    ],
    "operation": "AND"
  }]
}
```

#### Branching Rule (with success/failure paths)

```jsonc
{
  "mapTo": "AssetDescPositionWrapperKey",
  "conditionGroup": [
    {
      "id": "1",
      "condition": [{ "id": "R28", "dataKey": "showAssetDescription" }],
      "conditionValue": "true",
      "operation": "Equals",
      "nextGroupWithSuccess": "2",       // If true → go to group 2
      "nextGroupWithFailure": "3"        // If false → go to group 3 (hide)
    },
    {
      "id": "2",
      "condition": [{ "id": "R28", "dataKey": "assetDescription" }],
      "conditionValue": "",
      "operation": "Equals",
      "nextGroupWithSuccess": "4",       // If empty → show "No description"
      "nextGroupWithFailure": "5"        // If has value → show actual description
    },
    { "id": "3", "condition": [{ "id": "visibilityFalse" }] },
    { "id": "4", "condition": [{ "id": "R28", "dataKey": "noDescription" }] },
    { "id": "5", "condition": [{ "id": "R28", "dataKey": "assetDescription" }] }
  ]
}
```

**Branching flow visualized:**

```
showAssetDescription == "true"?
  ├── YES → assetDescription == ""?
  │         ├── YES → Show "No description" (fallback)
  │         └── NO  → Show actual assetDescription
  └── NO  → Hide (visibilityFalse)
```

#### Other ruleMapper entries in this widget

| mapTo | Purpose |
|-------|---------|
| `w4c6k2` | Asset media icon + color rules |
| `w4c6k2FallBack` | Fallback icon when thumbnail missing |
| `widgetBodyKey` | Toggle between error layout and content |
| `errorRuleKey` | Error state detection |
| `showMoreButtonKey` | Show more button text |
| `viewsVisibilityRule` | Hide views if count is 0 |
| `sharesVisibilityRule` | Hide shares if count is 0 |
| `WidgetTitlePositionWrapperKey` | Widget title with alignment |
| `AssetTitlePositionWrapperKey` | Asset title with alignment |
| `AssetDescPositionWrapperKey` | Description with show/hide/fallback |
| `AssetUpdatedOnPositionWrapperKey` | Updated/created date with show/hide |
| `AssetOwnerPositionWrapperKey` | Owner name with show/hide/fallback |
| `w4c6k0` | Expired tag rule |
| `assetAlignmentRule` | Asset alignment positioning |
| `assetRatingRule` | Rating display formatting |
| `(JS URL rule)` | App version check → padding adjustment |

---

### 4. data — API Request Definition

```jsonc
"data": {
  "id": "1587360209445203840",
  "name": "AssetLib - Single Asset Widget",
  "request": {
    "method": "POST",
    "protocol": "https",
    "host": "mobile.mindtickle.com",
    "pathname": "/equip/asset-hub-gql/graphql",

    "headers": [
      { "key": "x-token", "type": "TEXT", "value": { "stringValue": "{{x-token}}" } }
    ],

    "query": [
      { "key": "widget", "type": "TEXT", "value": { "stringValue": "getSingleAssetWidgetData" } }
    ],

    "environment": [
      { "key": "TRACK",         "type": "TEXT",  "value": { "stringValue": "integration" } },
      { "key": "forAdmin",      "type": "BOOL",  "value": { "boolValue": false } },
      { "key": "singleAssetId", "type": "TEXT",  "value": { "stringValue": "1751991775867136576" } },
      { "key": "x-token",       "type": "TEXT",  "value": { "stringValue": "GkxEULytbl0BJlvNj5Fbr78HM3d6D9qW" } }
    ],

    "body": "<GraphQL query string with variables>"
  }
}
```

---

### 5. layout — UI Component Tree

```jsonc
"layout": {
  "id": "1587359290628386688",
  "name": "AssetLib - Single Asset Widget",
  "localeUrl": "https://assets.mindtickle.com/.../translations/",
  "component": {
    "id": "//assets.mindtickle.com/.../SingleAsset/1/index.js",

    "mobileLayout": {                          // ROOT layout node
      "id": "w21",
      "type": "verticalStack",
      "childrens": [{ "component": "WidgetContent" }],
      "style": { "backgroundColor": "#FFFFFF", "padding": { "bottom": 8.0 } }
    },

    "childComponents": [                       // All nested component definitions
      // ... (see visual tree below)
    ]
  }
}
```

#### Visual Component Tree

```
verticalStack (root - w21, bg: #FFFFFF, padding-bottom: 8)
└── WidgetContent (zStack, key=errorRuleKey → toggles error vs body)
    └── WidgetBody (zStack)
        └── SingleAssetWidgetBody (verticalStack, padding: 16px all sides)
            │
            ├── HTMLTextComponent (widget title/content)
            │   key: WidgetTitlePositionWrapperKey
            │   font: OpenSans-Regular, 12pt, #525252
            │   expandable: true, lineLimit: 5
            │
            └── SingleAssetDetailWidgetComponent (verticalStack, TAPPABLE)
                │   key: assetAlignmentRule
                │   action: mobileAction URL
                │   tracking: { eventName, id, title }
                │
                ├── AssetImageComponent (zStack, 207x140, cornerRadius: 10)
                │   ├── ImageComponent (thumbnail, scaleToFill)
                │   │   key: categoriesThumbnail
                │   │
                │   ├── w4c14AssetLarge (horizontalStack, centered)
                │   │   └── IconFont (fallback icon, 32pt, #EB7863)
                │   │       key: w4c6k2FallBack, visibility: false by default
                │   │
                │   └── w4c8AssetLarge (verticalStack, absolute position, 100%x100%)
                │       ├── w4c9AssetLarge (horizontalStack)
                │       │   └── w4c6AssetLarge (expired tag badge)
                │       │       key: w4c6k0
                │       │       bg: #ffe1e1, 60x20, bottomRight corner radius
                │       │       └── TextComponent ("Expired", 10pt, #C92222)
                │       │
                │       ├── Spacer
                │       │
                │       └── w4c11AssetLarge (horizontalStack)
                │           └── w4c7AssetLarge (card, file type icon)
                │               bg: #ffffff, 30x26, topRight corner radius
                │               └── IconFont (16pt, #EB7863)
                │                   key: w4c6k2
                │
                ├── TextComponent (asset title)
                │   key: AssetTitlePositionWrapperKey
                │   font: OpenSans-SemiBold, 14pt, #2A2E36, Bold
                │   lineLimit: 2
                │
                ├── HTMLTextComponent (asset description)
                │   key: AssetDescPositionWrapperKey
                │   font: OpenSans-Regular, 12pt, #606369
                │   lineLimit: 5
                │
                ├── assetStatsComponent (horizontalStack)
                │   ├── [ImageComponent] viewsThumbnail (15x12)
                │   ├── [TextComponent]  viewCount (14pt, #606369)
                │   ├── [ImageComponent] sharesThumbnail (12x12)
                │   ├── [TextComponent]  shareCount (14pt, #606369)
                │   ├── [ImageComponent] ratingsThumbnail (12x12)
                │   └── [TextComponent]  assetRatingRule (14pt, #606369)
                │
                ├── TextComponent (last modified date)
                │   key: AssetUpdatedOnPositionWrapperKey
                │   font: OpenSans, 12pt, #989CA6
                │   lineLimit: 1
                │
                └── TextComponent (asset owner)
                    key: AssetOwnerPositionWrapperKey
                    font: OpenSans, 12pt, #989CA6
                    lineLimit: 1
```

#### Layout Types Reference

| Type | Description |
|------|-------------|
| `verticalStack` | Children stacked vertically (VStack) |
| `horizontalStack` | Children laid out horizontally (HStack) |
| `zStack` | Children overlaid on top of each other (ZStack) |

#### Style Properties Reference

| Property | Example | Description |
|----------|---------|-------------|
| `font.family` | `"OpenSans-SemiBold"` | Font family name |
| `font.size` | `14.0` | Font size in points |
| `font.color` | `"#2A2E36"` | Font color (hex) |
| `font.textStyle` | `"Bold"` | Text style |
| `backgroundColor` | `"#FFFFFF"` | Background color |
| `padding` | `{ "top": 16, "left": 16 }` | Padding per side |
| `cornerRadius` | `{ "radius": 10.0 }` | Corner rounding |
| `cornerRadius.corners` | `["bottomRight"]` | Selective corners |
| `border` | `{ "color": "#E8EAED", "width": 1.0 }` | Border styling |
| `size` | `{ "width": 207, "height": 140 }` | Fixed dimensions |
| `size.widthPercentage` | `99.9` | Percentage width |
| `align` | `"Center"`, `"leading"`, `"trailing"` | Alignment |
| `scaleMode` | `"scaleToFill"`, `"scaleToFit"` | Image scaling |
| `lineLimit` | `2` | Max text lines |
| `isExpandable` | `true` | Expandable text |
| `viewPosition` | `"absolute"` | Absolute positioning |
| `visibility` | `true`/`false` | Component visibility (in config) |

---

### 6. externals

```jsonc
"externals": [
  {
    "key": "__widget_name_learner__",
    "type": "TEXT",
    "value": { "stringValue": "Bookmarked" }
  }
]
```

---

### 7. config

```jsonc
"config": [
  { "key": "hideIfEmpty",              "type": "BOOL", "value": { "boolValue": false } },
  { "key": "noLayoutMinHeight",        "type": "TEXT", "value": { "stringValue": "248px" } },
  { "key": "topLinkType",             "type": "TEXT", "value": { "stringValue": "text" } },
  { "key": "topLinkText",             "type": "TEXT", "value": { "stringValue": "See All" } },
  { "key": "topLink",                 "type": "TEXT", "value": { "stringValue": "{{topLink}}" } },
  { "key": "showDescription",         "type": "BOOL", "value": { "boolValue": true } },
  { "key": "showDescriptionInTooltip","type": "BOOL", "value": { "boolValue": true } },
  { "key": "hideOnErrors",            "type": "TEXT", "value": { "stringValue": "401" } },
  { "key": "minHeight",               "type": "TEXT", "value": { "stringValue": "188px" } },
  { "key": "showTotalCount",          "type": "BOOL", "value": { "boolValue": true } }
]
```

---

### 8. environment

```jsonc
"environment": {
  "widget": [
    { "key": "topLink", "type": "TEXT", "value": { "stringValue": "#/callai/recordings?search=navKey%3DmyRecordings" } }
  ],
  "layout": [
    { "key": "showDetailsOnHover", "type": "BOOL", "value": { "boolValue": false } }
  ],
  "request": [
    { "key": "categoryId", "type": "TEXT", "value": { "stringValue": "4" } }
  ]
}
```

---

## Global Shared Definitions

### baseComponents (68 total)

Reusable component definitions shared across widgets.

```jsonc
{
  "id": "AssetImageComponent",
  "type": "card",
  "key": "w4c4k1",
  "layout": {
    "id": "w4L5",
    "type": "zStack",
    "childrens": [
      { "component": "ImageComponent", "key": "categoriesThumbnail", "style": { "align": "Center", "scaleMode": "scaleToFill" } },
      { "component": "w4c14AssetLarge" },
      { "component": "w4c8AssetLarge", "style": { "align": "leading" } }
    ]
  }
}
```

### rules (149 total)

Conditional rules that map input values to output styles/data.

```jsonc
{
  "id": "placeholderTextColor",
  "dataKey": "w2c4k3",
  "dataType": "style",
  "dataSet": {
    "null": { "font": { "family": "OpenSans", "size": 12.0, "color": "#989CA6" } },
    "":     { "font": { "family": "OpenSans", "size": 12.0, "color": "#989CA6" } }
  }
}
```

### analytics

```jsonc
"analytics": {
  "schema": {
    "companySchema": "iglu:com.mindtickle.platform/Company/jsonschema/1-0-1",
    "userSchema":    "iglu:com.mindtickle.platform/User/jsonschema/1-0-0",
    "widgetSchema":  "iglu:com.mindtickle.platform/Widget/jsonschema/1-0-0",
    "moduleSchema":  "iglu:com.mindtickle.platform/Module/jsonschema/1-0-0"
  }
}
```

### pageConfigs & sectionConfigs

```jsonc
"pageConfigs": {
  "default": { "backgroundColor": "#F5F6F7", "dividerHeight": 8.0 }
}

"sectionConfigs": {
  "default": {
    "backgroundColor": "#FFFFFF",
    "dividerHeight": 1.0,
    "dividerBackgroundColor": "#E8EAED",
    "dividerPaddingLeft": 16,
    "dividerPaddingRight": 16
  }
}
```

---

## Data Flow Summary

```
1. API Request (data.request)
   POST GraphQL to /equip/asset-hub-gql/graphql
         |
         v
2. mobileMapper
   Extracts values from API response into local keys
   e.g., "data.widgetLibrary.loadSingleAssetWidget.asset.name" → "assetName"
         |
         v
3. ruleMapper
   Applies conditional logic (branching, equality checks) to produce derived keys
   e.g., showAssetDescription==true && description!="" → show actual description
         |
         v
4. layout (Component Tree)
   Renders UI using resolved keys for text, images, visibility, and styles
   Components reference keys like "AssetTitlePositionWrapperKey" for their content
         |
         v
5. action + tracking
   Tap events trigger navigation via mobileAction URL
   Analytics tracked with eventName, eventId, eventTitle
```

---

## Key Concepts

- **Server-Driven UI**: The entire widget rendering (layout, data fetching, conditional rules, styling, and actions) is defined in this JSON, allowing the mobile app to render any widget without code changes.
- **mobileMapper**: The bridge between API responses and the UI — maps deep JSON paths to flat local keys.
- **ruleMapper**: A mini decision-tree engine — supports AND/OR operations, equality/greater-than checks, and branching with success/failure paths.
- **layout**: A recursive component tree using `verticalStack`, `horizontalStack`, and `zStack` as layout containers, with leaf components like `TextComponent`, `ImageComponent`, `HTMLTextComponent`, and `IconFont`.
- **keys**: Components reference `key` values which are resolved at runtime by the ruleMapper or directly from mobileMapper, enabling dynamic content and conditional visibility.
