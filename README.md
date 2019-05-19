It is a helper tool for Azure Cognitive Service Personalizer. It is not official.
https://docs.microsoft.com/en-us/azure/cognitive-services/personalizer/

Install
```sh
pip install azpersonalizer
```

Example
```python
import azpersonalizer

api_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
service_endpoint = "https://xxxxxx.api.cognitive.microsoft.com/"

def get_actions():
    actions = [
        {
            "id": "pasta",
            "features": [
                {
                "taste": "salty",
                "spiceLevel": "medium"
                },
                {
                "nutritionLevel": 5,
                "cuisine": "italian"
                }
            ]
        },
        {
            "id": "ice cream",
            "features": [
                {
                "taste": "sweet",
                "spiceLevel": "none"
                },
                {
                "nutritionLevel": 2
                }
            ]
        },
        {
            "id": "juice",
            "features": [
                {
                "taste": "sweet",
                "spiceLevel": "none"
                },
                {
                "nutritionLevel": 5
                },
                {
                "drink": True
                }
            ]
        },
        {
            "id": "salad",
            "features": [
                {
                "taste": "salty",
                "spiceLevel": "low"
                },
                {
                "nutritionLevel": 8
                }
            ]
        }
    ]
    return actions

def get_exclude_actions():
    actions = [
        "juice"
    ]
    return actions

def get_users_features(time_of_day_index, taste_index):
    time_of_day_features = ["morning", "afternoon", "evening", "night"]
    taste_features = ["salty", "sweet"]
    features = [
        {
          "time": time_of_day_features[time_of_day_index]
        },
        {
          "taste": taste_features[taste_index]
        }
    ]
    return features

# create client for personalizer
client = azpersonalizer.PersonalizerClient(api_key, service_endpoint)

# create rank resuest
rank_request = client.create_empty_rank_request()
rank_request["contextFeatures"] = get_users_features(0, 0) # 0:morning, 0:salty
rank_request["actions"] = get_actions()
rank_request["excludedActions"] = get_exclude_actions()

event_id = client.create_event_id()
# rank
result = client.rank(event_id, rank_request)
print(result.json()["rewardActionId"])
print(result.json())
# reward if you like
reward_request = client.create_empty_reward_request()
reward_request["value"] = 1
result = client.reward(event_id, reward_request)
```
