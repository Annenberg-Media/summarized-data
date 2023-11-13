# Optimized JSON Structure for Summarized Content Large Dataset

This README documents the rationale and structure for the optimized JSON format used in our summarizer project to handle large datasets.

## Rationale

The primary considerations for optimizing the JSON structure are:

- **Storage Efficiency**: A flattened structure consumes less space.
- **Access Speed**: Less nested structures mean faster read/write operations.
- **Scalability**: A simplified format is more manageable as the dataset grows.

## Structure

The optimized JSON structure employs a key-value mapping, where each unique key corresponds to a list of summary points, attribution type (0,1,2...) and attribution details (with a dateTime and sourceList)


### Before Optimization

Initially, the structure included nested objects and verbose keys:

```json
{
    "unique_key": {
        "summary": {
            "first_bullet": "Detail 1",
            "second_bullet": "Detail 2",
            "third_bullet": "Detail 3"
        }
    }
}
```


### After Optimization

To streamline the format, we've made the following changes:

- Flattened the structure to remove nested objects.
- Utilized date-based keys for organization and easy retrieval.
- Introduced a "attr" field which indicates type of summary (0:AI generated, 1: Human generated, 2: AI+Human generated)
- Introduced a "attrDetails" field with a "dateTime" value (pick current ISO datetime from https://greenwichmeantime.com/articles/clocks/iso/), and a "sourceList" array / list of sources of summarization (eg: GPT-4, Bard, Graham Stecklein etc.) in order of attribution importance
- Introduced a "summary" field that directly holds an array / list of summary points.
  
Here is the new structure:

```json
{
    "YYYY/MM/DD/unique-article-slug": {
        "attr":0,
        "attrDetails":{
            "dateTime":"2023-11-13T20:32:14Z",
            "sourceList":[
                "source 1",
                "source 2",
                "source 3"
            ]
        },
        "summary": [
            "Summary Bullet Point 1", 
            "Summary Bullet Point 2", 
            "Summary Bullet Point 3"
        ]
    }
}
```

### Example Entry

With the optimized structure, a sample JSON entry looks like this:

```json
{
    "2023/11/08/the-coliseum-one-of-the-most-electric-and-historic-stadiums-ive-got-the-chance-to-shine-in": {
        "attr":0,
        "attrDetails":{
            "dateTime":"2023-11-13T20:32:14Z",
            "sourceList":[
                "GPT-4"
            ]
        },
        "summary": [
            "Former USC football players celebrate the Coliseum's 100th anniversary, reminiscing about its electric atmosphere and a notable victory against Notre Dame in 2016.", 
            "The players share personal milestones, like scoring first touchdowns and a season's final home game, praising the unwavering fan support.", 
            "The Coliseum's historic legacy and the exhilarating experience of its game-day tunnel distinguish it from other stadiums."
        ]
    }
}
```

### Usage

To extract data from the optimized JSON structure, you would access the "summary" field of the relevant date-keyed object:

```javascript
let eventSummary = jsonData["2023/11/01/trick-or-strike-csu-faculty-vote-to-authorize-strike"].summary;
let firstBullet = eventSummary[0]; // "aa"
```

Note that there could be more than 3 summary bullet points also!
