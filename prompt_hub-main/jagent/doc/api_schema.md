## Message

Not all fields are required — leave unused fields as `null`. This message will be presented to a real user in a UI. Treat each field **strictly according to the following schema and example codes**. 

| Field               | Type                           | Description                                                                                                                                                                                               |
| ------------------- | ------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `text`              | string                         | can **only** be used for `XPost.content`, `XPost.translation`, `TimelineEvent.overview`                                                                                                                   |
| `lang`              | string                         | the language of the message.                                                                                                                                                                              |
| `source`            | string                         | links to the original content                                                                                                                                                                             |
| `sentiment`         | string                         | ‘bullish’, ‘bearish’ or ‘neutral’. In `NewsAnalysis` and `XPostMentionedProject` **.**                                                                                                                    |
| `tx_list`           | []$Tx                          | `[]Tx` should be processed in the following order: `tx.asset`→`tx.dir,` so that txs in each meassage share the same `trading_token`, and tx direction. seperate more asset and dir in different messages. |
| `x_post_list`       | []$XPost                       |                                                                                                                                                                                                           |
| `trading_token`     | Project                        | `the token topic of the message, acquired via access(’GetProject’)`                                                                                                                                       |
| `multi_factor`      | $MultiFactor                   | **Each message contains no more than 1 buy/sell point**, when there are multiple point, return multiple message.                                                                                          |
| `smart_signals`     | []$SmartSignal                 |                                                                                                                                                                                                           |
| `topic`             | string                         | user defined topic matched with this message. Limited Scenario: 1. match with XPost.content or XPost.translation via `semanticMatch`, or 2. as `keyword` to the args of `GetNewsAnalysis`                 |
| `x_interactions`    | []$XInteraction                |                                                                                                                                                                                                           |
| `similar_tokens`    | $SimilarTokens                 | `similar_tokens` are tokens that are similiar to a specified main token.                                                                                                                                  |
| `authentic_creator` | $CheckMemeTokenCreatorResponse | via **`CheckMemeTokenCreator`**                                                                                                                                                                           |

**Message examples**

**In the following cases, you must restrict your output message format as the examples.**

```jsx
// a user defined topic that is mentioned by at least 1 Twitter users (multi-language version).
const messageList = [
    {
        topic: '', // user defined topic which matches $XPost.content
        lang: 'en-US', // inferred from $XPost.language
        x_post_list: [
            {
                id: "", // $XPost.id
                language: "", // $XPost.language
                content: "", // $XPost.content
                author: {} // $XPost.author
            }
        ], // array of $XPost that matches the topic, sharing the same language
    },
    {
        topic: '', // user defined topic which matches $XPost.content
        lang: 'zh-CN', // inferred from $XPost.language
        x_post_list: [
            {
                id: "", // $XPost.id
                language: "", // $XPost.language
                content: "", // $XPost.content
                author: {} // $XPost.author
            }
        ], // array of $XPost that matches the topic, sharing the same language
    }
    // can have more language versions...
];

// a user defined topic/intent that is mentioned by $TimelineEvent (multi-language version).
const messageList = [
    {
        topic: '', // user defined topic/intent which matches $TimelineEvent.overview
        lang: 'en-US', // always en-US in this case
        source: '', // $TimelineEvent.url
        text: '', // $TimelineEvent.overview
    }
    // TimelineEvent is usually English only, but can add more languages if needed
];

// a user defined project that is mentioned by at least 1 twitter users (multi-language version).
const messageList = [
    {
        trading_token: {
            symbol: '', // Ticker symbol of the project
            ca: '', // Contract address of the project
            name: '', // Full name of the project
            logo: 0, // Logo identifier
            price: 0.0, // Current price in USD
            market_cap: 0.0, // Current market capitalization in USD
            volume_24h: 0.0, // 24-hour trading volume in USD
            holders: 0, // holders of the token of project
            liquidity: 0.0 // liquidity of the token of project
        }, // $Project that x_post_list is about
        lang: 'en-US', // inferred from $XPost.language
        x_post_list: [
            {
                id: "", // $XPost.id
                language: "", // $XPost.language
                content: "", // $XPost.content
                author: {} // $XPost.author
            }
        ], // array of $XPost that mention the project in $XPost.mentioned_projects, sharing the same language
    },
    {
        trading_token: {
            symbol: '', // Ticker symbol of the project
            ca: '', // Contract address of the project
            name: '', // Full name of the project
            logo: 0, // Logo identifier
            price: 0.0, // Current price in USD
            market_cap: 0.0, // Current market capitalization in USD
            volume_24h: 0.0, // 24-hour trading volume in USD
            holders: 0, // holders of the token of project
            liquidity: 0.0 // liquidity of the token of project
        }, // $Project that x_post_list is about
        lang: 'zh-CN', // inferred from $XPost.language
        x_post_list: [
            {
                id: "", // $XPost.id
                language: "", // $XPost.language
                content: "", // $XPost.content
                author: {} // $XPost.author
            }
        ], // array of $XPost that mention the project in $XPost.mentioned_projects, sharing the same language
    }
    // can have more language versions...
];

// a user defined project that is mentioned by $TimelineEvent (multi-language version).
const messageList = [
    {
        trading_token: {
            symbol: '', // Ticker symbol of the project
            ca: '', // Contract address of the project
            name: '', // Full name of the project
            logo: 0, // Logo identifier
            price: 0.0, // Current price in USD
            market_cap: 0.0, // Current market capitalization in USD
            volume_24h: 0.0, // 24-hour trading volume in USD
            holders: 0, // holders of the token of project
            liquidity: 0.0 // liquidity of the token of project
        }, // $Project user defined project mentioned by $TimelineEvent.overview
        lang: 'en-US', // always en-US in this case
        source: '', // $TimelineEvent.url
        text: '', // $TimelineEvent.overview
    }
    // TimelineEvent is usually English only, but can add more languages if needed
];

// get transaction list of a specific token (multi-language version)
const messageList = [
    {
        trading_token: {
            symbol: '', // Ticker symbol of the project
            ca: '', // Contract address of the project
            name: '', // Full name of the project
            logo: 0, // Logo identifier
            price: 0.0, // Current price in USD
            market_cap: 0.0, // Current market capitalization in USD
            volume_24h: 0.0, // 24-hour trading volume in USD
            holders: 0, // holders of the token of project
            liquidity: 0.0 // liquidity of the token of project
        }, // $Project that the tx_list is about
        lang: 'en-US', // always en-US in this case
        tx_list: [], // array of $Tx, note that all $Tx.asset===$Project.symbol, otherwise split into more messages
    }
    // Transaction data is usually English, but can add other language versions for localized display if needed
];

// get similar token list of a specific token (multi-language version)
const messageList = [
    {
        trading_token: {
            symbol: '', // Ticker symbol of the project
            ca: '', // Contract address of the project
            name: '', // Full name of the project
            logo: 0, // Logo identifier
            price: 0.0, // Current price in USD
            market_cap: 0.0, // Current market capitalization in USD
            volume_24h: 0.0, // 24-hour trading volume in USD
            holders: 0, // holders of the token of project
            liquidity: 0.0 // liquidity of the token of project
        }, // $Project that acts as the leading token of similar_tokens
        lang: 'en-US', // always en-US in this case
        similar_tokens: {
            count: 10,
            tokens: []
        }, // array of $Project that are similar to the trading_token, according to their name and symbol
    }
    // Similar token data is usually English, but can add other language versions for localized display if needed
];

// the user asks for technical analysis of a specific token (multi-language version)
const messageList = [
    {
        trading_token: {
            symbol: '', // Ticker symbol of the project
            ca: '', // Contract address of the project
            name: '', // Full name of the project
            logo: 0, // Logo identifier
            price: 0.0, // Current price in USD
            market_cap: 0.0, // Current market capitalization in USD
            volume_24h: 0.0, // 24-hour trading volume in USD
            holders: 0, // holders of the token of project
            liquidity: 0.0 // liquidity of the token of project
        }, // $Project that the analysis about
        lang: 'en-US', // always en-US in this case
        multi_factor: {
            kind: '', // buy or sell
            execute_price: 0, // the price at which the user wants to execute the order
            execute_date: '', // the unix timestamp at which the user wants to execute the order
            symbol_id: '', // the symbol id of the trading token, e.g. BINANCE_SPOT_BTC_USDT
            symbol: '', // the symbol of the trading token, e.g. BTC
            factors: [
                {
                    name: '', // the name of the factor, e.g. rsi of BTC
                    value: '', // the value of the factor, e.g. 0.5
                }
            ], // array of Factor that make up the execution of the order
        }
    }
    // Technical analysis is usually English, but can add other language versions for localized display if needed
];
```

If no meaningful message can be generated, you need to write a **fallback summary** in **human-readable Markdown** in the `fallback_msg` field:

```jsx
const message = {
	fallback_msg: 'this is a fallback info of message'
}
```

## FetchResponse

| Field     | Type     | Notes                                             |
| --------- | -------- | ------------------------------------------------- |
| `status`  | int      | http status code                                  |
| `headers` | object   | http response header                              |
| `json`    | function | return the JSON.parsed result of the http call    |
| `text`    | function | return the plain result of the http call as text. |

## MultiFactor

| **Field**       | **Type**  | **Description**                      | **Example**             |
| --------------- | --------- | ------------------------------------ | ----------------------- |
| `kind`          | enum      | Trading action type, "buy" or "sell" |                         |
| `execute_price` | number    | Trade execution price                | 50000.00                |
| `execute_date`  | number    | Trade execution timestamp            | 1646092800              |
| `symbol_id`     | string    | Trading pair identifier              | "BINANCE_SPOT_BTC_USDT" |
| `symbol`        | string    | Token symbol                         | "BTC"                   |
| `factors`       | []$Factor | Array of factor objects              |                         |

## Factor

| **Field** | **Type** | **Description**               | **Example**                                  |
| --------- | -------- | ----------------------------- | -------------------------------------------- |
| `name`    | string   | Name of the factor            | "BTC Exchange Netflow"                       |
| `value`   | string   | Factor description with value | "BTC Exchange Netflow is positive (1234.56)" |

## SimilarTokens

| Field    | Type       | Notes |
| -------- | ---------- | ----- |
| `tokens` | []$Project |       |

## MessageCategory

| Field  | Type   | Notes |
| ------ | ------ | ----- |
| `name` | string |       |
| `desc` | string |       |

## XInteraction

| Field         | Type         | Notes              |
| ------------- | ------------ | ------------------ |
| `kind`        | string       | ‘follow’,’mention’ |
| `description` | string       |                    |
| `from`        | $TwitterUser |                    |
| `to`          | $TwitterUser |                    |
| `post`        | $XPost       |                    |
| `created_at`  | int64        |                    |

## TimelineEvent

| Field        | Type   | Description              |
| ------------ | ------ | ------------------------ |
| `event_time` | int64  | event occured time       |
| `url`        | string | url                      |
| `title`      | string | title of the event       |
| `overview`   | string | llm summarized overview  |
| `sentiment`  | string | BULLISH,BEARISH, NEUTRAL |

## Wallet

| Field      | Type            | Description                     |
| ---------- | --------------- | ------------------------------- |
| `address`  | string          |                                 |
| `network`  | string          | network of the address to query |
| `identity` | $WalletIdentity | identity of wallet              |
| `labels`   | $WalletLabels   | labels of wallet                |

## WalletLabels

| Field      | Type     | Description     |
| ---------- | -------- | --------------- |
| `behavior` | []string | Behavior labels |
| `custom`   | []string | Custom labels   |

## WalletIdentity

| Field                    | Type   | Description              |
| ------------------------ | ------ | ------------------------ |
| `kind`                   | enum   | Identity kind of wallet: |
| KOL/VC/WHALE/SMART_MONEY |
| `name`                   | string | Identity name of wallet  |
| `twitter`                | string | Twitter handle of wallet |

## Tx

note price of the symbol can be calculated via `amount_usd/amount`

| Field                   | Type    | Description                                       |
| ----------------------- | ------- | ------------------------------------------------- |
| `dir`                   | enum    | Direction of the transaction ('bought' or 'sold') |
| `wallet_addr`           | string  | Address of the wallet involved in the transaction |
| `asset_addr`            | string  | Contract address of the asset being traded        |
| `asset`                 | string  | Symbol or name of the asset being traded          |
| `asset_entity_url`      | string  | URL to the asset's entity page                    |
| `side_asset`            | string  | Symbol or name of the other asset in the trade    |
| `side_asset_addr`       | string  | Contract address of the other asset in the trade  |
| `side_asset_entity_url` | string  | URL to the other asset's entity page              |
| `amount`                | float64 | Amount of the asset traded                        |
| `amount_usd`            | float64 | USD value of the transaction                      |
| `time`                  | int64   | Timestamp of the transaction                      |
| `source`                | string  | Source of the transaction data                    |
| `wallet_name`           | string  | Name or label of the wallet                       |
| `wallet_kind`           | string  | Type or category of the wallet                    |
| `network`               | string  | Blockchain network where the transaction occurred |

## Project

| Field          | Type    | Description                                      |
| -------------- | ------- | ------------------------------------------------ |
| `symbol`       | string  | Ticker symbol of the project                     |
| `ca`           | string  | Contract address of the project                  |
| `name`         | string  | Full name of the project                         |
| `logo`         | int64   | Logo identifier                                  |
| `id`           | int64   | Unique project identifier                        |
| `network`      | string  | Blockchain network where the project is deployed |
| `network_logo` | string  | URL of the network logo                          |
| `price`        | float64 | Current price in USD                             |
| `market_cap`   | float64 | Current market capitalization in USD             |
| `volume_24h`   | float64 | 24-hour trading volume in USD                    |
| `holders`      | int64   | holders of the token of project                  |
| `liquidity`    | float64 | liquidity of the token of project                |
| `background`   | string  | meme token background/story                      |
| `owner`        | string  | meme token creator/owner                         |

## SmartSignal

| Field        | Type             | Description                                           |
| ------------ | ---------------- | ----------------------------------------------------- |
| `id`         | int64            | Unique identifier of the signal                       |
| `kind`       | $SmartSignalKind | Type of the signal (e.g., PRICE_ANOMALY, SOCIAL_HEAT) |
| `content`    | string           | Detailed content of the signal                        |
| `icon`       | string           | URL or identifier of the signal icon                  |
| `language`   | string           | Language code of the signal content                   |
| `project_id` | int64            | ID of the project this signal is for                  |
| `symbol`     | string           | Ticker symbol of the project                          |
| `created_at` | int64            | Timestamp when the signal was created                 |

## SmartSignalKind

| Kind                                                                                                                    | NOTE |
| ----------------------------------------------------------------------------------------------------------------------- | ---- |
| PRICE_ANOMALY,OPEN_INTEREST_ANOMALY,FUNDING_RATE_ANOMALY,MARKET_CAP_ANOMALY,TRADING_VOLUME_ANOMALY,TOKEN_HOLDER_ANOMALY |      |
| SMART_MONEY_MOVE,WHALE_MOVE,KOL_MOVE,VC_MOVE                                                                            |      |
| ALVA_BULLISH                                                                                                            |      |
| SOCIAL_HEAT                                                                                                             |      |
| SENTIMENT                                                                                                               |      |
| TOKEN_UNLOCK                                                                                                            |      |
| AIRDROP                                                                                                                 |      |
| ANNOUNCEMENT,BIG_NEWS,DAO_PROPOSAL                                                                                      |      |
| SMART_FOLLOWER_INTERACTION,SMART_FOLLOWER_CHANGE                                                                        |      |
| FLASH_NEWS                                                                                                              |      |
|                                                                                                                         |      |

## TwitterUser

| Field              | Type   | Description                       |
| ------------------ | ------ | --------------------------------- |
| `id`               | string | Twitter user ID                   |
| `username`         | string | Twitter username (without @)      |
| `name`             | string | Display name of the user          |
| `description`      | string | User's bio or description         |
| `avatar`           | string | URL of the user's profile picture |
| `followers_count`  | int64  | Number of followers               |
| `followings_count` | int64  | Number of accounts being followed |

## XPostMentionedProject

| Field       | Type   | Notes                                |
| ----------- | ------ | ------------------------------------ |
| `id`        | int64  | Unique id of the project             |
| `name`      | string |                                      |
| `symbol`    | string | symbol ticker of the project         |
| `sentiment` | string | sentiment of the project in the post |
| `digest`    | string |                                      |

## XReferencedPost

| Field        | Type         | Description                                                     |
| ------------ | ------------ | --------------------------------------------------------------- |
| `author`     | $TwitterUser | Author information of the post                                  |
| `id`         | string       | Unique identifier of the post                                   |
| `content`    | string       | Original content of the post                                    |
| `created_at` | int64        | Timestamp when the post was created                             |
| `type`       | string       | Type of tweet being cited，include：replied_to/quoted/retweeted |

## XPostMetrics

| Field              | Type  | Description                                     |
| ------------------ | ----- | ----------------------------------------------- |
| `retweet_count`    | int64 | The number of times the post has been forwarded |
| `reply_count`      | int64 | The number of replies received by the post      |
| `like_count`       | int64 | The number of likes the post has received       |
| `quote_count`      | int64 | The number of times the post has been cited.    |
| `bookmark_count`   | int64 | The number of times the post has been favorited |
| `impression_count` | int64 | The number of times the post has been viewed    |

## XPost

| Field              | Type               | Description                                    |
| ------------------ | ------------------ | ---------------------------------------------- |
| `author`           | $TwitterUser       | Author information of the post                 |
| `id`               | string             | Unique identifier of the post                  |
| `content`          | string             | Original content of the post                   |
| `created_at`       | int64              | Timestamp when the post was created            |
| `referenced_posts` | []$XReferencedPost | Other tweets associated with the current tweet |
| `metrics`          | $XPostMetrics      | Metrics of the post                            |

## Bar

| Field          | Type    | Description                 |
| -------------- | ------- | --------------------------- |
| `date`         | int64   | Timestamp of the data point |
| `open`         | float64 | Opening price               |
| `high`         | float64 | Highest price               |
| `low`          | float64 | Lowest price                |
| `close`        | float64 | Closing price               |
| `volume`       | float64 | Trading volume              |
| `trades`       | int64   | Number of trades            |
| `taker_volume` | float64 | Volume of taker trades      |

## TsData

| Field   | Type    | Description                 |
| ------- | ------- | --------------------------- |
| `date`  | int64   | Timestamp of the data point |
| `value` | float64 | Value of the metric         |

## LiquidationData

| Field   | Type    | Description                      |
| ------- | ------- | -------------------------------- |
| `short` | float64 | liquidated short position in usd |
| `long`  | float64 | liquidated long position in usd  |

## VolumeData

| Field  | Type    | Description        |
| ------ | ------- | ------------------ |
| `buy`  | float64 | buy volume in usd  |
| `sell` | float64 | sell volume in usd |

## FundingRateWithIntervalData

| Field                   | Type    | Description                    |
| ----------------------- | ------- | ------------------------------ |
| `funding_rate`          | float64 | funding rate value             |
| `funding_rate_interval` | int32   | funding rate interval in hours |

## MemeToken

| Field          | Type               | Description                                    |
| -------------- | ------------------ | ---------------------------------------------- |
| `name`         | string             | Name of the token                              |
| `symbol`       | string             | Ticker symbol of the token                     |
| `contract`     | string             | Contract address of the token                  |
| `network`      | string             | Blockchain network where the token is deployed |
| `creator`      | $MemeToken_Creator | Creator information                            |
| `social_media` | $SocialMedia       | Social media links                             |
| `logo`         | string             | URL of the token logo                          |
| `uri`          | string             | Token URI                                      |
| `date_time`    | int64              | Timestamp when the token was created           |

## MemeToken_Creator

| Field     | Type   | Description                   |
| --------- | ------ | ----------------------------- |
| `name`    | string | Name of the token creator     |
| `avatar`  | string | URL of the creator's avatar   |
| `address` | string | Wallet address of the creator |

## SocialMedia

| Field         | Type   | Description               |
| ------------- | ------ | ------------------------- |
| `x`           | string | Twitter/X profile URL     |
| `discord`     | string | Discord server URL        |
| `twitter`     | string | Twitter profile URL       |
| `gitbook`     | string | GitBook documentation URL |
| `website`     | string | Project website URL       |
| `telegram`    | string | Telegram group URL        |
| `galxe_space` | string | Galxe space URL           |
| `medium`      | string | Medium blog URL           |

## CheckMemeTokenCreatorResponse

| Field      | Type         | Description                                                 |
| ---------- | ------------ | ----------------------------------------------------------- |
| `verified` | bool         | Whether the token was created by the specified Twitter user |
| `reason`   | string       | Detailed message about the verification result              |
| `user`     | $TwitterUser | Twitter profile information of the creator                  |

## SimilarTokens

| Field    | Type       | Description             |
| -------- | ---------- | ----------------------- |
| `count`  | int64      | Count of similar tokens |
| `tokens` | []$Project | Similar tokens          |

## MemeCreator

| Field            | Type       | Description          |
| ---------------- | ---------- | -------------------- |
| `address`        | string     | Meme creator address |
| `network`        | string     | Meme creator network |
| `created_tokens` | []$Project | Token create history |

## Document

| Field          | Type   | Description                  |
| -------------- | ------ | ---------------------------- |
| `title`        | string | Title of the document        |
| `content`      | string | Content of the document      |
| `url`          | string | URL of the document          |
| `publish_time` | int64  | Publish time of the document |
