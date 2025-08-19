# 🚀 Daily Portfolio and Credit Spread Screeners

"I am now AI, start with $400, ChatGPT vs Grok, I will do whatever they say.  I am not responsible for my actions.  No DD.  No Brains.  JUST VIBES!"  

---
# 🛠 Configure TastyTrade

Steps located in `Configure TastyTrade`


# 1️⃣ Select a 72 ticker trading universe

## Download the Trading Universe

`XLK` https://www.sectorspdrs.com/mainfund/XLK

`XLC` https://www.sectorspdrs.com/mainfund/XLC

`XLY` https://www.sectorspdrs.com/mainfund/XLY

`XLP` https://www.sectorspdrs.com/mainfund/XLP

`XLV` https://www.sectorspdrs.com/mainfund/XLV

'XLF` https://www.sectorspdrs.com/mainfund/XLF

`XLI` https://www.sectorspdrs.com/mainfund/XLI

`XLE` https://www.sectorspdrs.com/mainfund/XLE

`XLU` https://www.sectorspdrs.com/mainfund/XLU


## Prompt [gpt5|grok4]
```
Use the attached ticker basket files as the universe.
Select the top 4 tickers per sector/theme for trading 0–45 DTE credit spreads today.
Apply this strict filter framework (real-time only):
  1. Earnings & Macro Events (Scheduled) – Must verify in today’s/week’s earnings calendars or official macro event schedules (Fed, CPI, jobs, OPEC, regulatory). Exclude if unverified.
  2. Headline & News Drivers – Must be sourced from live headlines (upgrades/downgrades, strikes, lawsuits, product launches, sector disruptions). Rank by strength of catalyst.
  3. Implied Volatility Context (Event-Driven) – Only flag if real-time news or analyst notes explicitly cite elevated IV or “fear premium.” Ignore historical averages.
  4. Directional Tilt – Classify bias as bullish, bearish, or neutral only if justified by current event/news flow. If unclear, mark as “Neutral.”
  5. Shock Disconnection / Factor Buckets  – Ensure coverage across growth (Tech/Discretionary), rates (Financials/Utilities), commodities (Energy/Industrials), and defensives (Staples/Healthcare). Avoid clustering.

Output_1 format (table):
  Sector | Ticker | Event/News Driver (1 short sentence, real-time) | Tilt (Bullish/Bearish/Neutral)

Output_2 format (portfolio):

A) PYTHON_PATCH
```python
SECTORS_GPT = {
    "Information Technology": {
        "etf": "XLK",
        "description": "growth/innovation beta",
        "tickers": ["T1","T2","T3","T4"],
    },
    "Communication Services": {
        "etf": "XLC",
        "description": "ads, platforms, media",
        "tickers": ["T1","T2","T3","T4"],
    },
    "Consumer Discretionary": {
        "etf": "XLY",
        "description": "cyclical demand, sentiment",
        "tickers": ["T1","T2","T3","T4"],
    },
    "Consumer Staples": {
        "etf": "XLP",
        "description": "defensive cashflows, low vol",
        "tickers": ["T1","T2","T3","T4"],
    },
    "Health Care": {
        "etf": "XLV",
        "description": "defensive + policy/innovation mix",
        "tickers": ["T1","T2","T3","T4"],
    },
    "Financials": {
        "etf": "XLF",
        "description": "rate curve/credit sensitivity",
        "tickers": ["T1","T2","T3","T4"],
    },
    "Industrials": {
        "etf": "XLI",
        "description": "capex, global trade, PMIs",
        "tickers": ["T1","T2","T3","T4"],
    },
    "Energy": {
        "etf": "XLE",
        "description": "commodity/inflation shock hedge",
        "tickers": ["T1","T2","T3","T4"],
    },
    "Utilities": {
        "etf": "XLU",
        "description": "bond-proxy, duration sensitivity",
        "tickers": ["T1","T2","T3","T4"],
    },
}


Rules:
  1. Use only real-time, verifiable data.
  2. Exclude any ticker where data cannot be confirmed.
  3. Look ahead for scheduled events today/this week.
```
---

# 2️⃣ Daily Portfolio Screener

## `sectors.py`

```python
open sectors.py
```
Remember to copy and paste the output from prompt output directly above into `sectors.py` and save it before executing the workflow below.

## `build_universe.py`


## `spot.py`


## `atm_iv.py`


## `liquidity.py`


## `run_pipeline.py`

```python
python3 runpipeline.py
```

Run this script to pick the daily trading tickers.  9 sectors, 4 tickers per sectors.  ChatGPT and Grok have each selected their own tickers from the ETF's listed above.

---

# 3️⃣ Daily Credit Spread Screener


## `stock_prices.py`


## `options_chains.py`


## `iv_data.py`


## `market_prices.py`


## `risk_analysis.py`

---

# ✒️ IV and Liquidity Analysis 


## 📁 Find High IV and Liquidity

### Data: each option’s open interest, trading volume, bid-ask spread, and existing IV.  
### Why useful: These numbers show how busy the contract is and how cheap it is to trade.

**Create:** `touch iv_liquidity.py`

**Query:** `touch iv_liquidity.py`

```bash
# advanced_iv_liquidity.py - STEP 6: Fixed version with better data collection
import json
import numpy as np
from datetime import datetime
import asyncio
from tastytrade import Session, DXLinkStreamer
from tastytrade.dxfeed import Summary
from config import USERNAME, PASSWORD

async def analyze_iv_and_liquidity():
    print("📊 STEP 6: Advanced IV & Liquidity Analysis")
    print("=" * 70)
    print("🎯 Collecting comprehensive market data...")
    
    # Load previous data
    with open('step1_stock_prices.json', 'r') as f:
        stock_data = json.load(f)
    
    with open('step2_options_contracts.json', 'r') as f:
        options_data = json.load(f)
    
    with open('step3_iv_data.json', 'r') as f:
        iv_data = json.load(f)
    
    with open('step4_market_prices.json', 'r') as f:
        price_data = json.load(f)
    
    print(f"✅ Loaded data: {options_data['total_contracts_found']} contracts to analyze")
    
    session = Session(USERNAME, PASSWORD)
    
    # Create lookups
    price_lookup = {}
    for company, prices_list in price_data['prices_by_company'].items():
        for price in prices_list:
            price_lookup[price['contract_name']] = price
    
    companies = list(stock_data['stock_prices'].keys())
    enhanced_options = {}
    
    # Collect ALL contract symbols
    all_symbols = []
    symbol_to_company = {}
    
    for company, company_data in options_data['options_by_company'].items():
        for exp_data in company_data['expiration_dates'].values():
            for contract in exp_data['contracts']:
                symbol = contract['streamer_symbol']
                all_symbols.append(symbol)
                symbol_to_company[symbol] = company
    
    print(f"📡 Need to get Summary data for {len(all_symbols)} contracts...")
    
    # Process in batches to avoid overwhelming the connection
    batch_size = 500
    summary_data = {}
    
    async with DXLinkStreamer(session) as streamer:
        for batch_start in range(0, len(all_symbols), batch_size):
            batch_end = min(batch_start + batch_size, len(all_symbols))
            batch_symbols = all_symbols[batch_start:batch_end]
            
            print(f"\n   📊 Processing batch {batch_start//batch_size + 1}/{(len(all_symbols) + batch_size - 1)//batch_size}")
            print(f"      Symbols {batch_start + 1} to {batch_end} of {len(all_symbols)}")
            
            # Subscribe to this batch
            await streamer.subscribe(Summary, batch_symbols)
            
            # Collect data for this batch
            batch_collected = 0
            start_time = asyncio.get_event_loop().time()
            no_data_timeout = 0
            
            # Collect for up to 30 seconds per batch or until we stop getting new data
            while (asyncio.get_event_loop().time() - start_time) < 30 and no_data_timeout < 5:
                try:
                    summary = await asyncio.wait_for(streamer.get_event(Summary), timeout=1.0)
                    if summary and summary.event_symbol in batch_symbols:
                        summary_data[summary.event_symbol] = {
                            'open_interest': int(summary.open_interest) if summary.open_interest else 0,
                            'volume': int(summary.prev_day_volume) if summary.prev_day_volume else 0,
                            'day_high': float(summary.day_high_price) if summary.day_high_price else 0,
                            'day_low': float(summary.day_low_price) if summary.day_low_price else 0
                        }
                        batch_collected += 1
                        no_data_timeout = 0  # Reset timeout counter
                        
                        if batch_collected % 50 == 0:
                            print(f"      Collected: {batch_collected} summaries")
                        
                except asyncio.TimeoutError:
                    no_data_timeout += 1
                    continue
                except Exception as e:
                    continue
            
            print(f"      ✅ Batch complete: {batch_collected} summaries collected")
            
            # Unsubscribe from this batch before moving to next
            await streamer.unsubscribe(Summary, batch_symbols)
            
            # Small delay between batches
            await asyncio.sleep(0.5)
    
    print(f"\n✅ Total Summary data collected: {len(summary_data)} contracts")
    
    # Now analyze each company with all the data
    for company in companies:
        print(f"\n🏢 Analyzing {company}...")
        
        company_options = options_data['options_by_company'].get(company, {})
        if not company_options:
            continue
        
        current_price = company_options['current_stock_price']
        company_contracts = []
        
        # Stats tracking
        stats = {
            'total_analyzed': 0,
            'has_iv': 0,
            'has_price': 0,
            'has_summary': 0,
            'has_all_data': 0
        }
        
        for exp_data in company_options['expiration_dates'].values():
            for contract in exp_data['contracts']:
                symbol = contract['streamer_symbol']
                stats['total_analyzed'] += 1
                
                # Check data availability
                has_iv = symbol in iv_data['iv_by_contract']
                has_price = symbol in price_lookup
                has_summary = symbol in summary_data
                
                if has_iv:
                    stats['has_iv'] += 1
                if has_price:
                    stats['has_price'] += 1
                if has_summary:
                    stats['has_summary'] += 1
                
                # Only analyze if we have at least IV and price data
                if not (has_iv and has_price):
                    continue
                
                stats['has_all_data'] += 1
                
                # Get all data
                current_iv = iv_data['iv_by_contract'][symbol]
                price_info = price_lookup[symbol]
                
                # Get summary data or use defaults
                if has_summary:
                    liquidity = summary_data[symbol]
                else:
                    liquidity = {'open_interest': 0, 'volume': 0}
                
                # Calculate metrics
                bid = price_info['what_buyers_pay']
                ask = price_info['what_sellers_want']
                spread = ask - bid
                
                # Calculate liquidity score
                liquidity_score = calculate_liquidity_score(
                    liquidity['open_interest'],
                    liquidity['volume'],
                    spread,
                    company
                )
                
                contract_analysis = {
                    'symbol': symbol,
                    'strike': contract['strike_price'],
                    'type': contract['contract_type'],
                    'days_to_exp': contract['days_until_expires'],
                    'current_iv': current_iv,
                    'open_interest': liquidity['open_interest'],
                    'volume': liquidity['volume'],
                    'bid': bid,
                    'ask': ask,
                    'bid_ask_spread': spread,
                    'liquidity_score': liquidity_score,
                    'liquid': liquidity_score >= 70,
                    'has_summary_data': has_summary
                }
                
                company_contracts.append(contract_analysis)
        
        # Calculate company metrics
        if company_contracts:
            liquid_contracts = [c for c in company_contracts if c['liquid']]
            high_volume = [c for c in company_contracts if c['volume'] >= 100]
            high_oi = [c for c in company_contracts if c['open_interest'] >= 1000]
            tight_spreads = [c for c in company_contracts if c['bid_ask_spread'] <= 0.10]
            
            avg_iv = np.mean([c['current_iv'] for c in company_contracts])
            
            enhanced_options[company] = {
                'current_stock_price': current_price,
                'avg_implied_volatility': avg_iv,
                'data_coverage': stats,
                'metrics': {
                    'total_contracts_analyzed': len(company_contracts),
                    'liquid_contracts': len(liquid_contracts),
                    'high_volume_contracts': len(high_volume),
                    'high_oi_contracts': len(high_oi),
                    'tight_spread_contracts': len(tight_spreads),
                    'contracts_with_summary': sum(1 for c in company_contracts if c['has_summary_data'])
                },
                'top_liquid_contracts': sorted(
                    company_contracts, 
                    key=lambda x: (x['liquidity_score'], x['open_interest']), 
                    reverse=True
                )[:20]
            }
            
            print(f"   📊 Avg IV: {avg_iv:.3f}")
            print(f"   📈 Contracts analyzed: {len(company_contracts)}")
            print(f"   💧 Liquid contracts: {len(liquid_contracts)}")
            print(f"   📊 High OI (≥1000): {len(high_oi)}")
            print(f"   📊 Summary data coverage: {stats['has_summary']}/{stats['total_analyzed']} ({stats['has_summary']/stats['total_analyzed']*100:.1f}%)")
    
    # Find best opportunities
    all_liquid_contracts = []
    for company, data in enhanced_options.items():
        for contract in data['top_liquid_contracts']:
            if contract['liquid']:
                contract['company'] = company
                all_liquid_contracts.append(contract)
    
    # Sort by liquidity score
    all_liquid_contracts.sort(key=lambda x: (x['liquidity_score'], x['open_interest']), reverse=True)
    
    # Save results
    result = {
        'step': 6,
        'what_we_did': 'Comprehensive IV & Liquidity Analysis',
        'timestamp': datetime.now().isoformat(),
        'data_summary': {
            'total_contracts': options_data['total_contracts_found'],
            'contracts_with_iv': len(iv_data['iv_by_contract']),
            'contracts_with_prices': price_data['total_prices_collected'],
            'contracts_with_summary': len(summary_data)
        },
        'companies_analyzed': len(enhanced_options),
        'enhanced_options': enhanced_options,
        'top_liquid_contracts': all_liquid_contracts[:50],
        'liquidity_criteria': {
            'score_threshold': 70,
            'oi_threshold': 1000,
            'volume_threshold': 100,
            'spread_thresholds': {
                'top_names': 0.05,
                'others': 0.10
            }
        }
    }
    
    filename = 'step6_advanced_iv_liquidity.json'
    with open(filename, 'w') as f:
        json.dump(result, f, indent=2)
    
    print(f"\n✅ Analysis complete!")
    print(f"📊 Summary data coverage: {len(summary_data)}/{len(all_symbols)} contracts ({len(summary_data)/len(all_symbols)*100:.1f}%)")
    print(f"💧 Total liquid contracts found: {len(all_liquid_contracts)}")
    print(f"📁 Results saved to: {filename}")
    
    return result

def calculate_liquidity_score(open_interest, volume, spread, company):
    """Calculate 0-100 liquidity score"""
    score = 0
    
    # Open interest component (40 points)
    if open_interest >= 1000:
        score += 40
    elif open_interest >= 500:
        score += 30
    elif open_interest >= 100:
        score += 20
    elif open_interest > 0:
        score += min(20, (open_interest / 100) * 20)
    
    # Volume component (30 points)
    if volume >= 1000:
        score += 30
    elif volume >= 500:
        score += 20
    elif volume >= 100:
        score += 10
    elif volume > 0:
        score += min(10, (volume / 100) * 10)
    
    # Spread component (30 points)
    if spread >= 0:
        if company in ['NVDA', 'TSLA', 'AMZN']:
            if spread <= 0.05:
                score += 30
            elif spread <= 0.10:
                score += 20
            elif spread <= 0.20:
                score += 10
        else:
            if spread <= 0.10:
                score += 30
            elif spread <= 0.20:
                score += 20
            elif spread <= 0.50:
                score += 10
    
    return min(100, score)

if __name__ == "__main__":
    asyncio.run(analyze_iv_and_liquidity())
```
**Run:** `python3 iv_liquidity.py`

# ✒️ Black Scholes Analysis


## 📁 Find the Best Deals

### Data: liquid call + put pairs with bid/ask, IV, open interest, strike width, days to expire.  
### Why useful: calculates credit, max loss, ROI, and probability to profit.

**Create:** `touch find_tendies.py`

**Query:** `open -e find_tendies.py`

```bash
# enhanced_find_tendies.py - STEP 7: Both Call and Put Credit Spreads
import json
import numpy as np
from datetime import datetime
from scipy.stats import norm

class EliteCreditSpreadScanner:
    """Advanced credit spread scanner for BOTH calls and puts"""
    
    def __init__(self, risk_free_rate=0.05):
        self.risk_free_rate = risk_free_rate
    
    def black_scholes_probability(self, S, K, T, sigma, option_type='call'):
        """Calculate probability of staying OTM for calls or puts"""
        if T <= 0 or sigma <= 0:
            return 0
        
        d2 = (np.log(S / K) + (self.risk_free_rate - 0.5 * sigma**2) * T) / (sigma * np.sqrt(T))
        
        if option_type == 'call':
            # Probability call stays OTM (stock stays below K)
            return norm.cdf(-d2) * 100
        else:
            # Probability put stays OTM (stock stays above K)  
            return norm.cdf(d2) * 100
    
    def scan_call_spreads(self, liquid_contracts, current_price, company, price_lookup, avg_iv):
        """Scan for bear call credit spreads (calls above current price)"""
        call_spreads = []
        
        # Get calls above current price
        calls_above = []
        for contract in liquid_contracts:
            if (contract['type'] == 'CALL' and 
                contract['strike'] > current_price and
                contract['liquid'] and
                contract['symbol'] in price_lookup):
                calls_above.append(contract)
        
        calls_above.sort(key=lambda x: x['strike'])
        
        # Create call spreads
        for i in range(len(calls_above) - 1):
            short_call = calls_above[i]
            long_call = calls_above[i + 1]
            
            spread = self.analyze_credit_spread(
                short_call, long_call, current_price, company, 
                price_lookup, avg_iv, 'BEAR_CALL'
            )
            if spread:
                call_spreads.append(spread)
        
        return call_spreads
    
    def scan_put_spreads(self, liquid_contracts, current_price, company, price_lookup, avg_iv):
        """Scan for bull put credit spreads (puts below current price)"""
        put_spreads = []
        
        # Get puts below current price
        puts_below = []
        for contract in liquid_contracts:
            if (contract['type'] == 'PUT' and 
                contract['strike'] < current_price and
                contract['liquid'] and
                contract['symbol'] in price_lookup):
                puts_below.append(contract)
        
        puts_below.sort(key=lambda x: x['strike'], reverse=True)  # Highest to lowest
        
        # Create put spreads
        for i in range(len(puts_below) - 1):
            short_put = puts_below[i]      # Higher strike (short)
            long_put = puts_below[i + 1]   # Lower strike (long)
            
            spread = self.analyze_credit_spread(
                short_put, long_put, current_price, company, 
                price_lookup, avg_iv, 'BULL_PUT'
            )
            if spread:
                put_spreads.append(spread)
        
        return put_spreads
    
    def analyze_credit_spread(self, short_option, long_option, current_price, 
                            company, price_lookup, avg_iv, spread_type):
        """Analyze a credit spread (works for both calls and puts)"""
        
        strike_width = abs(long_option['strike'] - short_option['strike'])
        if strike_width > 10:
            return None
        
        short_symbol = short_option['symbol']
        long_symbol = long_option['symbol']
        
        # Get price data
        short_price = price_lookup[short_symbol]
        long_price = price_lookup[long_symbol]
        
        # Calculate credit (what we collect)
        credit = short_price['what_buyers_pay'] - long_price['what_sellers_want']
        if credit <= 0:
            return None
        
        max_risk = strike_width - credit
        roi = (credit / max_risk * 100) if max_risk > 0 else 0
        
        # Skip low ROI
        if roi < 10:
            return None
        
        # Get IV for probability calculation
        short_iv = short_option.get('current_iv', avg_iv)
        time_to_exp = short_option['days_to_exp'] / 365
        
        # Calculate probability based on spread type
        if spread_type == 'BEAR_CALL':
            # For bear call: want stock to stay BELOW short strike
            prob_profit = self.black_scholes_probability(
                current_price, short_option['strike'], time_to_exp, short_iv, 'call'
            )
            distance_from_money = ((short_option['strike'] - current_price) / current_price) * 100
        else:  # BULL_PUT
            # For bull put: want stock to stay ABOVE short strike  
            prob_profit = self.black_scholes_probability(
                current_price, short_option['strike'], time_to_exp, short_iv, 'put'
            )
            distance_from_money = ((current_price - short_option['strike']) / current_price) * 100
        
        # Skip low probability
        if prob_profit < 65:
            return None
        
        # Check minimum liquidity
        min_oi = min(short_option['open_interest'], long_option['open_interest'])
        if min_oi < 500:
            return None
        
        return {
            'company': company,
            'spread_type': spread_type,
            'short_strike': short_option['strike'],
            'long_strike': long_option['strike'],
            'strike_width': strike_width,
            'days_to_expiration': short_option['days_to_exp'],
            'credit': credit,
            'max_risk': max_risk,
            'roi_percent': roi,
            'probability_of_profit': prob_profit,
            'current_stock_price': current_price,
            'distance_from_money': distance_from_money,
            'short_iv': short_iv,
            'min_open_interest': min_oi,
            'short_symbol': short_symbol,
            'long_symbol': long_symbol,
            'strategy_explanation': self.get_strategy_explanation(spread_type, short_option['strike'], long_option['strike'])
        }
    
    def get_strategy_explanation(self, spread_type, short_strike, long_strike):
        """Explain the strategy"""
        if spread_type == 'BEAR_CALL':
            return f"Sell ${short_strike} call, buy ${long_strike} call. Profit if stock stays below ${short_strike}"
        else:
            return f"Sell ${short_strike} put, buy ${long_strike} put. Profit if stock stays above ${short_strike}"

def scan_all_credit_spreads():
    print("🏆 STEP 7: Complete Credit Spread Scanner")
    print("=" * 70)
    print("🎯 Scanning BOTH Call and Put Credit Spreads...")
    print("📈 Bear Call Spreads: Profit when stock doesn't go UP")
    print("📉 Bull Put Spreads: Profit when stock doesn't go DOWN")
    
    # Load all data
    with open('step1_stock_prices.json', 'r') as f:
        stock_data = json.load(f)
    
    with open('step2_options_contracts.json', 'r') as f:
        options_data = json.load(f)
    
    with open('step3_iv_data.json', 'r') as f:
        iv_data = json.load(f)
    
    with open('step4_market_prices.json', 'r') as f:
        price_data = json.load(f)
    
    with open('step6_advanced_iv_liquidity.json', 'r') as f:
        iv_liquidity_data = json.load(f)
    
    scanner = EliteCreditSpreadScanner()
    
    # Create price lookup
    price_lookup = {}
    for company, prices_list in price_data['prices_by_company'].items():
        for price in prices_list:
            price_lookup[price['contract_name']] = price
    
    all_spreads = []
    call_spreads_total = 0
    put_spreads_total = 0
    
    # Scan each company
    for company, company_options in options_data['options_by_company'].items():
        current_price = company_options['current_stock_price']
        company_iv_data = iv_liquidity_data['enhanced_options'].get(company, {})
        avg_iv = company_iv_data.get('avg_implied_volatility', 0.3)
        
        print(f"\n🏢 Scanning {company} (Price: ${current_price:.2f}, Avg IV: {avg_iv:.3f})...")
        
        # Skip if IV too low
        if avg_iv < 0.25:
            print(f"   ⚠️ IV too low ({avg_iv:.3f}), skipping...")
            continue
        
        liquid_contracts = company_iv_data.get('top_liquid_contracts', [])
        
        # Scan call credit spreads (bear call spreads)
        call_spreads = scanner.scan_call_spreads(
            liquid_contracts, current_price, company, price_lookup, avg_iv
        )
        call_spreads_total += len(call_spreads)
        
        # Scan put credit spreads (bull put spreads)  
        put_spreads = scanner.scan_put_spreads(
            liquid_contracts, current_price, company, price_lookup, avg_iv
        )
        put_spreads_total += len(put_spreads)
        
        all_spreads.extend(call_spreads)
        all_spreads.extend(put_spreads)
        
        print(f"   📈 Bear Call Spreads: {len(call_spreads)}")
        print(f"   📉 Bull Put Spreads: {len(put_spreads)}")
        print(f"   🎯 Total for {company}: {len(call_spreads) + len(put_spreads)}")
    
    # Sort by ROI * Probability score
    for spread in all_spreads:
        spread['combined_score'] = spread['roi_percent'] * (spread['probability_of_profit'] / 100)
    
    all_spreads.sort(key=lambda x: x['combined_score'], reverse=True)
    
    print(f"\n💎 TOTAL CREDIT SPREADS FOUND: {len(all_spreads)}")
    print(f"📈 Bear Call Spreads: {call_spreads_total}")
    print(f"📉 Bull Put Spreads: {put_spreads_total}")
    print("=" * 70)
    
    # Show top 15 - mixed calls and puts
    print(f"\n🏆 TOP 15 CREDIT SPREADS (Both Types):")
    print("-" * 120)
    
    for i, spread in enumerate(all_spreads[:15]):
        spread_icon = "📈" if spread['spread_type'] == 'BEAR_CALL' else "📉"
        spread_name = "Bear Call" if spread['spread_type'] == 'BEAR_CALL' else "Bull Put"
        
        print(f"{i+1:2}. {spread_icon} {spread['company']:4} {spread_name:9} | "
              f"${spread['short_strike']:.0f}/{spread['long_strike']:.0f} | "
              f"Score: {spread['combined_score']:.1f} | "
              f"PoP: {spread['probability_of_profit']:.1f}% | "
              f"ROI: {spread['roi_percent']:.1f}% | "
              f"Credit: ${spread['credit']:.2f} | "
              f"DTE: {spread['days_to_expiration']}")
        print(f"     📝 {spread['strategy_explanation']}")
    
    # Save results
    result = {
        'step': 7,
        'what_we_did': 'Complete Credit Spread Analysis - Both Calls and Puts',
        'timestamp': datetime.now().isoformat(),
        'total_spreads_found': len(all_spreads),
        'bear_call_spreads': call_spreads_total,
        'bull_put_spreads': put_spreads_total,
        'all_spreads': all_spreads[:100],  # Top 100
        'summary_stats': {
            'avg_roi': np.mean([s['roi_percent'] for s in all_spreads]) if all_spreads else 0,
            'avg_probability': np.mean([s['probability_of_profit'] for s in all_spreads]) if all_spreads else 0,
            'avg_combined_score': np.mean([s['combined_score'] for s in all_spreads]) if all_spreads else 0
        }
    }
    
    filename = 'step7_complete_credit_spreads.json'
    with open(filename, 'w') as f:
        json.dump(result, f, indent=2)
    
    print(f"\n✅ Saved complete analysis to: {filename}")
    
    # Show strategy breakdown
    if all_spreads:
        best_call = next((s for s in all_spreads if s['spread_type'] == 'BEAR_CALL'), None)
        best_put = next((s for s in all_spreads if s['spread_type'] == 'BULL_PUT'), None)
        
        print(f"\n💎 STRATEGY COMPARISON:")
        if best_call:
            print(f"   📈 Best Bear Call: {best_call['company']} ${best_call['short_strike']:.0f}/{best_call['long_strike']:.0f}")
            print(f"      ROI: {best_call['roi_percent']:.1f}%, PoP: {best_call['probability_of_profit']:.1f}%")
        
        if best_put:
            print(f"   📉 Best Bull Put: {best_put['company']} ${best_put['short_strike']:.0f}/{best_put['long_strike']:.0f}")
            print(f"      ROI: {best_put['roi_percent']:.1f}%, PoP: {best_put['probability_of_profit']:.1f}%")
    
    return result

if __name__ == "__main__":
    scan_all_credit_spreads()
```

**Run:** `python3 find_tendies.py`

---

## 📁 Step 7: The Master Script

**Create:** `touch master.py`

**Query:** `open -e master.py`


```bash
import asyncio
import subprocess
import os
from datetime import datetime

async def run_complete_analysis():
    print("🤖 MASTER TRADING ROBOT - COMPLETE CREDIT SPREAD SYSTEM")
    print("=" * 80)
    print("🚀 Running complete credit spread analysis in 7 steps...")
    print("📈 Finding BOTH Bear Call and Bull Put Credit Spreads")
    print("⏰ This will take about 8-10 minutes total")
    print("🧮 Using Black-Scholes with real market data")
    print("=" * 80)
    
    steps = [
        ("stock_prices.py", "Getting current stock prices"),
        ("options_chains.py", "Finding all options contracts"), 
        ("iv_data.py", "Collecting implied volatility data"),
        ("market_prices.py", "Getting real-time bid/ask prices"),
        ("risk_analysis.py", "Analyzing Greeks and risk metrics"),
        ("iv_liquidity.py", "Advanced IV & liquidity analysis"),
        ("find_tendies.py", "Elite credit spread scanner")
    ]
    
    start_time = datetime.now()
    
    for i, (script, description) in enumerate(steps, 1):
        print(f"\n🎯 STEP {i}/7: {description}")
        print(f"🏃‍♂️ Running {script}...")
        
        try:
            # Run the script and wait for it to finish
            result = subprocess.run(['python3', script], 
                                  capture_output=True, 
                                  text=True, 
                                  timeout=300)  # 5 minute timeout
            
            if result.returncode == 0:
                print(f"   ✅ Step {i} completed successfully!")
                # Print some of the output so we can see progress
                if result.stdout:
                    lines = result.stdout.strip().split('\n')
                    # Show last few meaningful lines
                    meaningful_lines = [line for line in lines[-6:] if line.strip() and not line.startswith('   ')]
                    for line in meaningful_lines[-3:]:  # Show last 3 meaningful lines
                        if '✅' in line or '💎' in line or '🏆' in line or 'Found' in line:
                            print(f"      {line}")
            else:
                print(f"   ❌ Step {i} failed!")
                print(f"   Error: {result.stderr}")
                if result.stdout:
                    print(f"   Output: {result.stdout}")
                return False
                
        except subprocess.TimeoutExpired:
            print(f"   ⏰ Step {i} took too long (over 5 minutes)")
            return False
        except Exception as e:
            print(f"   ❌ Error running step {i}: {e}")
            return False
    
    end_time = datetime.now()
    total_time = (end_time - start_time).total_seconds()
    
    print(f"\n🎉 ALL STEPS COMPLETED!")
    print("=" * 80)
    print(f"⏰ Total time: {total_time/60:.1f} minutes")
    print(f"📁 Files created:")
    print(f"   📊 step1_stock_prices.json")
    print(f"   🎰 step2_options_contracts.json") 
    print(f"   📈 step3_iv_data.json")
    print(f"   💰 step4_market_prices.json")
    print(f"   🧮 step5_risk_analysis.json")
    print(f"   📊 step6_advanced_iv_liquidity.json")
    print(f"   🏆 step7_elite_spreads.json")
    
    # Show final summary from the complete credit spread analysis
    try:
        import json
        with open('step7_elite_spreads.json', 'r') as f:
            final_data = json.load(f)
        
        print(f"\n🏆 COMPLETE CREDIT SPREAD RESULTS:")
        print(f"   🧮 Model: Black-Scholes with real market data")
        print(f"   📊 Total opportunities: {final_data['total_spreads_found']}")
        print(f"   📈 Bear Call Spreads: {final_data['bear_call_spreads']}")
        print(f"   📉 Bull Put Spreads: {final_data['bull_put_spreads']}")
        
        if final_data.get('elite_spreads') and len(final_data['elite_spreads']) > 0:
            best_spread = final_data['elite_spreads'][0]
            
            print(f"\n   🥇 BEST ELITE SPREAD:")
            print(f"      📈 {best_spread['company']} Bear Call ${best_spread['short_strike']:.0f}/{best_spread['long_strike']:.0f}")
            print(f"      💰 Credit: ${best_spread['credit']:.2f}")
            print(f"      📊 Probability: {best_spread['probability_of_profit']:.1f}%")
            print(f"      💎 ROI: {best_spread['roi_percent']:.1f}%")
            print(f"      🏆 Master Score: {best_spread['master_score']:.1f}/100")
            print(f"      📅 Days to expiration: {best_spread['days_to_expiration']}")
            
            # Show top 3 elite spreads
            top_spreads = final_data['elite_spreads'][:3]
            print(f"\n   🏆 TOP 3 ELITE SPREADS:")
            for i, spread in enumerate(top_spreads, 1):
                print(f"      {i}. {spread['company']} ${spread['short_strike']:.0f}/{spread['long_strike']:.0f}: Score {spread['master_score']:.1f}, {spread['probability_of_profit']:.1f}% PoP, {spread['roi_percent']:.1f}% ROI")
        
        # Show summary stats
        if 'summary_stats' in final_data:
            stats = final_data['summary_stats']
            print(f"\n   📊 SUMMARY STATISTICS:")
            print(f"      💰 Average ROI: {stats['avg_roi']:.1f}%")
            print(f"      📈 Average Probability: {stats['avg_probability']:.1f}%")
            print(f"      🏆 Average Master Score: {stats['avg_master_score']:.1f}/100")
            print(f"      🔥 Average IV: {stats['avg_iv']:.3f}")
    
    except Exception as e:
        print(f"   ⚠️ Could not load final summary: {e}")
        print(f"   📄 Check step7_elite_spreads.json for detailed results")
    
    print(f"\n🎯 COMPLETE TRADING SYSTEM SUMMARY:")
    print(f"   🔬 Mathematical Model: Black-Scholes option pricing")
    print(f"   📊 Data Sources: Real-time tastytrade market data")
    print(f"   📈 Strategies: Bear call spreads (profit when stock doesn't rise)")
    print(f"   🏆 Analysis: 5 legendary trader frameworks combined")
    print(f"   🛡️ Risk Management: Greeks analysis with full liquidity metrics")
    print(f"   💡 Probability: Log-normal distribution with real IV")
    print(f"   🎯 Filters: Master Score > 50, Probability > 65%, ROI > 10%")
    
    # Show which files to examine
    print(f"\n📂 NEXT STEPS:")
    print(f"   1. 🔍 Examine: step7_elite_spreads.json")
    print(f"   2. 📈 Look for: High master score + probability spreads")
    print(f"   3. 🛡️ Check: Liquidity and Greeks data")
    print(f"   4. 🏆 Focus on: Spreads with multiple trader signals")
    
    return True

if __name__ == "__main__":
    # Run the complete analysis system
    asyncio.run(run_complete_analysis())
```

**Run:** `python3 master.py`

---


# [GPT/GROK] Prompt

```text
You are my Credit-Spread Catalyst & Sanity Checker. Timezone: America/Los_Angeles.
Use absolute dates. When you fetch news/events, include links and sources.

INPUTS (paste below):
=== step7_complete_credit_spreads.json ===
{PASTE_JSON_HERE}
=== optional: step4_liquidity.json ===
{PASTE_JSON_HERE_OR_SKIP}
=== end ===

GOALS
For the top 20 spreads by combined_score:
  • Validate “sane to trade today?” across catalysts, liquidity, and calendar risk.
  • Surface reasons to Delay/Avoid (not advice—just risk signals).

CHECKLIST (per spread)
1) Calendar gates:
   - Earnings date between today and the spread’s expiration? Mark “Earnings-Inside-Trade”.
   - Ex-div date inside the trade window? Note potential assignment/price gap risk.
   - Sector macro events within 5 trading days (e.g., CPI/FOMC for Financials/Tech beta; OPEC/EIA for Energy; FDA calendar for biotech tickers). 
2) Fresh news (last 72h):
   - Pull 1–2 headlines that could move the underlying. Link them.
3) Liquidity sanity:
   - Confirm both legs have adequate OI (≥500 minimum; ≥1,000 preferred) and spreads not wider than 10¢ (tier-2) or 5¢ (tier-1 names). If step4_liquidity.json present, use Δ30 proxies; else infer from available fields.
4) Price sanity:
   - Credit ≤ width, ROI = credit/(width-credit). Recompute if needed; flag if odd (e.g., credit > width).
5) Risk note:
   - Summarize exposure (bear call = short upside; bull put = short downside) and distance-from-money (%). 
   - Note if IV regime seems low (<0.25) for premium selling or unusually high (>0.60) for gap risk.

OUTPUT FORMAT
- A ranked table with: 
  Ticker | Type (BearCall/BullPut) | Strikes | DTE | Credit | ROI% | Dist-OTM% | OI(min) | Spread sanity | Key Event(s) | Fresh News | Decision (Do / Delay / Avoid) + 1-line reason
- Then a short summary:
  • #Passing vs #Flagged 
  • Top 3 “Do” candidates with the clearest catalyst path (quiet calendar, sufficient OI, tight spreads)
  • Top 3 risk reasons observed (e.g., earnings inside window, macro landmines, thin OI)

RULES
- Information only; no trading advice. 
- Always include links for news/events you cite.
- If any required field is missing, mark “n/a” and continue; do not fabricate.
``` 

