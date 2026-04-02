# Trading Platform Overview

## Overview

The **CFD Cross-Margin Trading Platform** is a high-performance trading solution designed for a wide range of financial markets, including **forex**, **cryptocurrencies**, **commodities**, **indices**, **stocks**, and **ETFs**. Built for **brokers**, **proprietary trading firms**, and other **financial institutions**, the platform offers flexibility, scalability, and advanced risk management features. Initially built on top of a solid **isolated-margin** platform, the platform now empowers users to leverage the full potential of **cross-margin** trading, providing a comprehensive and versatile trading experience.

The platform’s **user group management**, **hedging mode**, and **flexible setup** make it a powerful tool for managing various trading environments. It is available as a **customizable web and mobile solution**, supporting **desktop web, mobile web, and mobile app (PWA)**, allowing traders to access their accounts and trade from any device. A strong advantage of the platform lies in its **technology stack**, which allows for **easy vertical and horizontal scaling**. This ensures that brokers, proprietary trading firms, and other financial institutions can grow their operations **on the fly**, handling increasing trading volumes without interruption.

The platform's **margin trading engine** now incorporates both **cross-margin** and **isolated-margin** models, offering brokers and traders flexible risk management solutions. It also offers **copy trading** as an additional product upon demand, enhancing its versatility for both novice and experienced traders.

Additionally, the platform is built with a **classic MetaTrader approach** in mind, making it highly compatible with MetaTrader systems. It uses familiar concepts such as **lot-based trading**, **PnL calculations**, and **margin management formulas**, ensuring that users migrating from MetaTrader find the transition seamless, both in terms of **concepts** and **trading logic**.

The platform is equipped with a **Dealing Administration Panel** that provides full control over user management, trading groups, execution settings, and more, making it highly versatile for **brokers**, **proprietary trading firms**, and other **financial institutions**.

## Key Features

- **Customizable Trading Platform**: The platform is fully customizable for **Desktop Web**, **Mobile Web**, and **Mobile Apps** (PWA). This allows brokers to tailor the interface and functionality according to their branding and trader requirements.
- **Advanced Margin Trading Engine**:
    - Built on top of an **isolated-margin** platform, with full support for **cross-margin** trading, offering flexibility based on user needs.
    - **Overnight Fee** feature for managing fees applied to positions held overnight.
- **MetaTrader-Compatible Trading Approach**: The platform is designed with a **classic MetaTrader approach** in mind. It supports **lot-based trading**, **PnL calculation formulas**, and **margin management logic** similar to MetaTrader. This makes the platform easy to migrate to for brokers and traders who are already familiar with MetaTrader’s ecosystem and operational logic.
- **Copy Trading**: Available as an additional product on demand, copy trading allows traders to follow and replicate the strategies of top performers, enhancing engagement and providing services for less experienced users.
- **Dual Execution Modes**: The platform supports multiple execution models, giving brokers flexibility in managing liquidity and risk. One mode ensures straight-through processing (STP) to liquidity providers, while the other allows for optimized in-house trade execution.
- **Datacenter Hosting**: Clients can choose their preferred hosting provider. The platform supports both **cloud solutions** and **dedicated environments**, offering flexible deployment options to meet performance and security needs.
- **Bridge Integration**: The platform supports **bridge integration** with third-party liquidity aggregators, connecting to multiple liquidity providers (LPs), ensuring deep liquidity and optimal execution.
- **Price Feeder with Multiple LP Support**: The platform includes a **price feeder system** that integrates with multiple liquidity providers (LPs), ensuring accurate and competitive pricing across all supported instruments.
- **KYC Providers Integration**: The platform integrates with KYC (Know Your Customer) providers to ensure regulatory compliance. This allows brokers to streamline user onboarding and verification processes, enhancing security and trust.
- **Effective Candles Store and Updates**: The platform offers an **effective candles storage and update system** that ensures historical and real-time price data is stored efficiently and updated in real time, allowing traders to perform precise technical analysis.
- **Full CRM Integration Capabilities**: The platform provides APIs for seamless integration with external **Customer Relationship Management (CRM)** systems. These APIs allow brokers to manage client data, monitor trading activity, and integrate with other systems like payment gateways, KYC providers, and reporting tools.
- **Payment Providers Integration**: The platform supports integration with various **payment providers**, allowing for smooth deposit and withdrawal processes, helping traders manage their funds with ease.
- **Scalable Technology Stack**: The platform's underlying technology stack is designed for **easy vertical and horizontal scaling**, allowing the system to handle increased traffic and trading volumes without interruptions. This scalability makes the platform suitable for both **growing brokers** and large **proprietary trading firms** or other **financial institutions** looking to expand their operations.

---

## Platform Architecture

The **CFD Cross-Margin Trading Platform** features a robust architecture optimized for **high-volume trading** and **real-time data processing**, ensuring low-latency execution and exceptional performance. It integrates seamlessly with third-party systems and allows brokers to manage operations efficiently through a flexible backend interface.

- **Core Trading Engine**: Handles order execution, margin calculations, PnL, and real-time risk management.
- **Bridge Integration**: Facilitates connections with multiple liquidity providers, ensuring competitive pricing and deep liquidity across supported markets.
- **Price Feeder System**: The integrated **price feeder** supports multiple liquidity providers (LPs), ensuring accurate and up-to-date pricing across all traded instruments.
- **Effective Candles Storage and Updates**: The platform stores historical candle data and updates real-time candles efficiently, allowing traders to conduct precise technical analysis and backtesting.
- **CRM and Payment Integration**: Provides full integration with **external CRMs** via API, allowing brokers to import and manage client data, execute trades, manage funds, and handle communications. Payment provider integration enables smooth financial operations, including deposits and withdrawals.
- **Cross-Margin and Isolated Margin Platforms**: The platform offers separate solutions for **cross-margin** and **isolated-margin** trading, ensuring flexibility for both brokers and proprietary traders depending on their risk management preferences.

---

## Cross-Margin Trading Model

The **cross-margin trading** model optimizes margin usage by allowing traders to utilize available balances across multiple positions and accounts, which helps minimize margin calls and forced liquidation during market volatility. Additionally, the platform offers a separate **isolated margin** model for traders who prefer to manage risk on a per-position basis, providing more control over individual trades.

- **Optimized Capital Use**: Cross-margining enables traders to pool margin across various positions, reducing the overall margin requirement and freeing up capital for other trades.
- **Reduced Risk of Liquidation**: By leveraging available margin across positions, cross-margin trading reduces the likelihood of margin calls or forced liquidation due to insufficient margin in volatile market conditions.
- **Flexible Leverage and Risk Management**: The platform automatically applies the lower of account- or instrument-level leverage, ensuring that traders operate within safe risk limits predefined by the broker.

---

## Multi-Account Functionality

The platform allows traders to manage multiple accounts under a single login, each configured with unique settings defined by the broker. These settings include **leverage**, **base currency**, and **trading profiles**, providing flexibility for traders who need different accounts for varied strategies or funds segregation.

- **Account Settings Defined by Broker**: The broker determines all key parameters such as leverage, instrument access, and trading profiles for each account.
- **Cross-Account Margin Use**: The cross-margin system ensures capital efficiency by utilizing available funds across multiple accounts when necessary.

---

## Dealing Administration Panel

The **Dealing Administration Panel** provides brokers with full control over the platform’s functionality, from managing user profiles to configuring trading groups and instruments. Key features include:

- **View & Manage Client Profiles**: Admins can view and manage client details, including trading activity, leverage updates, and account performance.
- **Setup Instruments & User Groups**: Brokers can configure financial instruments and set up user groups with specific trading profiles tailored to their needs.
- **Manage Execution & Routing Settings**: Full control over liquidity provider settings and execution routing ensures optimal trade execution.
- **Trading Groups & Profiles**: Define trading groups and execution profiles with customized parameters, such as margin settings and swap profiles.
- **Swap Profiles**: Admins can configure swap profiles for long and short positions, along with their respective schedules.
- **Manage Feeds & Liquidity Providers**: Ensure real-time pricing and execution through managed data feeds and liquidity provider integration.
- **Chart History Management**: Admins can upload and view historical chart data for use in technical analysis by traders.
- **Manage Positions**: Admins can monitor and manage active positions in real-time and review historical trades.
- **Balance & Bonus Operations**: Perform balance adjustments, apply bonus credits, and manage other financial operations for client accounts.

---

## Supported Markets and Instruments

The platform supports a broad range( 2,000+) of financial markets, including:

- **Forex**
- **Cryptocurrencies**
- **Commodities**
- **Indices**
- **Stocks**
- **ETFs**
- **and more**

Each instrument is fully configurable by the broker, allowing customization of **leverage**, **contract size**, **lot size**, **swap rates**, and other key trading parameters.