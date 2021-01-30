import csv
import tweepy

COMPANY_DATA="companies_output.csv"
CSV_FILE_NAME = "Job Leads-Daily - Tweet (2).csv"

# Rahul's Key
CONSUMER_KEY = "******"
CONSUMER_SECRET = "******"
ACCESS_TOKEN = "******"
ACCESS_TOKEN_SECRET = "******"

# REMOTE LEAF
CONSUMER_KEY = "******"
CONSUMER_SECRET = "***"
ACCESS_TOKEN = "******"
ACCESS_TOKEN_SECRET = "******"


auth = tweepy.OAuthHandler(CONSUMER_KEY, CONSUMER_SECRET)
auth.set_access_token(ACCESS_TOKEN, ACCESS_TOKEN_SECRET)

api = tweepy.API(auth)
original_tweet="""
Looking for a remote job? 🔎

Here’s a thread of some remote job openings in tech that can be done anywhere in the world, curated by us today! 

Please share with your friends who are looking for remote work in tech. 
👇

"""


status_with_handle = """
⚡ @{} is looking for a {} 

📍 Location:  Remote - {} 
👉 Apply here!

{} 
"""

status_without_handle = """
⚡ {} is looking for a {} 

📍 Location:  Remote - {} 
👉 Apply here!

{} 
"""

last_tweet = api.update_status(original_tweet)

def find_handle_from_company_list(company_url):
    print(f"Got a request to find the Twitter handle for the URL: {company_url}")
    if not company_url:
        return None
    with open(COMPANY_DATA,'r') as f:
        reader= csv.reader(f)
        next(reader)
        found = False
        handle = None
        for row in reader:
            if row[3] in company_url:
                found = True
                handle = row[21]
                break
        if found:
            print(f"Company details found for: {company_url}")
            print(handle)
            return handle
        else:
            print(f"Company details not found for: {company_url}")
            return None


with open(CSV_FILE_NAME, 'r') as file:
    reader = csv.reader(file)
    next(reader)
    for row in reader:
        print(f"Going to Tweet about the job opening {row[0]}")
        handle = find_handle_from_company_list(row[5])
        if handle:
            status = status_with_handle.format(handle, row[0], row[2], row[4])
        else:
            status = status_without_handle.format(row[1], row[0], row[2], row[4])

        last_tweet=api.update_status(status,in_reply_to_status_id=last_tweet.id,auto_populate_reply_metadada=True)
        print(f"Just Tweeted the message: {status}")
