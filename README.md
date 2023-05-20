# Sportsbetting
Algorithm to pull sportsbetting data from an API every 2 minutes for a total of 10 minutes


import requests
import time

api_key = "a3787744c4efb19646b939bed4ab305e"
sport_key = "basketball_nba"

url = f"https://api.the-odds-api.com/v4/sports/{sport_key}/odds/?apiKey={api_key}&regions=us&markets=h2h,spreads&oddsFormat=american"

results = []

for _ in range(5):  # Perform API calls 5 times with 2-minute intervals
    response = requests.get(url)

    if response.status_code != 200:
        raise Exception("Failed to retrieve odds")

    events = response.json()

    for event in events:
        home_team = event['home_team']
        away_team = event['away_team']
        if home_team in ['Los Angeles Lakers', 'Denver Nuggets'] or away_team in ['Los Angeles Lakers', 'Denver Nuggets']:
            print(f"Home Team: {home_team}")
            print(f"Away Team: {away_team}")
            for bookmaker in event['bookmakers']:
                if bookmaker['title'] == 'DraftKings':  # Specify the desired bookmaker
                    print(f"Bookmaker: {bookmaker['title']}")
                    print(f"Last Update: {bookmaker['last_update']}")
                    for market in bookmaker['markets']:
                        if market['key'] == 'h2h':
                            for outcome in market['outcomes']:
                                if outcome['name'] in ['Denver Nuggets', 'Los Angeles Lakers']:
                                    odds = outcome['price']
                                    if odds > 0:
                                        winnings = round(odds * 100 / 100, 2)
                                        probability = round(100 / (odds + 100), 2)
                                        print(f"Outcome: {outcome['name']}, Odds: {odds}, Potential Winnings: ${winnings}, Probability: {probability}")
                                    else:
                                        winnings = round(100 / abs(odds) * 100, 2)
                                        probability = round(abs(odds) / (abs(odds) + 100), 2)
                                        print(f"Outcome: {outcome['name']}, Odds: {odds}, Potential Winnings: ${winnings}, Probability: {probability}")
            print('\n')

    results.append(events)
    time.sleep(120)  # Wait for 2 minutes before the next API call

print("Results after 10 minutes:")
print(results)
