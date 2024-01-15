# Japanese Wikipedia Human Retrieval dataset

This is a Japanese question answereing dataset with retrieval on Wikipedia articles
by trained human workers.

## About the dataset

Each entry represents a single QA session:
given a question sentence, the responsible worker tried to search for the appropriate
information from Wikipedia using the search box and/or inner hyperlinks, and constructs
the answer paragraphs according to the search results.
The whole process of each retrieval is recorded manually by the same worker.

The dataset consists of following data:

* A question sentence
* The final answer paragraph (whole sentence, and chunks with citations)
* List of references with either extracted paragraph or summarization from a Wikipedia
article

As well as the successful session, we also recorded the failed session: the worker
failed to construct the answer from the search results. In this case we recorded
at least the retrieval process despite lack of the answer.

We release this version of the dataset with the following dataset chunks:

* "answered" chunk (838 examples): question, answer, and retrieval process
* "not_answered" chunk (433 examples): question and retrieval process (no answer)

## Data structure

Each entry has the following schema:

```js
{
    "id": number, // Entry ID
    "question": string, // Question sentence

    // Answer section
    // Absense of this field means that the worker failed to answer the question.
    "answer": {
        "text": string, // Answer paragraph

        // Answer sentences
        // The answer paragraph is generated by joining all texts in this list.
        "sentences": [
            {
                "text": string, // Answer sentence
                "citations": number[], // List of reference IDs for citation
            }
        ],
    },

    // Reference list
    "references": [
        {
            // Either "search" or "link" field exists.

            // Information for direct search (search box on Wikipedia)
            "search": {
                "keywords": string[], // List of words input into the search box
            },

            // Information for hyperlinks
            "link": {
                "referrer": number, // The reference ID at which the worker clicked the hyperlink
            }

            // Extracted content
            "page": {
                "title": string, // Title of the Wikipedia article
                "url": string, // URL of either Wikipedia article or search results

                // Either "quote" or "summary" field exists.
                // Absense of both field means that the page doesn't contain appropriate data.

                // Information for direct quotation
                // There could be multiple "page" fields with "quote" subfield if multiple
                // sentences are extracted from distant positions in the same page.
                "quote": {
                    "text": string, // Consecutive texts extracted from the article
                },

                // Information for summarization
                "summary": {
                    "text": string, // Summary text about the page written by the worker.
                    "method": string, // Description about how the worker wrote the summary.
                }
            }
        },
    ],
}
```

Example ("answered" data ID=1):

```json
{
    "id": 1,
    "question": "経済産業省の役割について知りたい。",
    "answer": {
        "text": "経済産業省は、日本の行政機関のひとつです。経済および産業の発展ならびに鉱物資源およびエネルギー資源の供給に関する行政を所管しています。民間の経済活力の向上及び対外経済関係の円滑な発展を中心とする経済及び産業の発展並びに鉱物資源及びエネルギー資源の安定的かつ効率的な供給の確保を図るために、マクロ経済政策、産業政策、通商政策、貿易管理業務、産業技術政策、流通政策、エネルギー政策などを所管しています。",
        "sentences": [
            {
                "text": "経済産業省は、日本の行政機関のひとつです。",
                "citations": [
                    0
                ]
            },
            {
                "text": "経済および産業の発展ならびに鉱物資源およびエネルギー資源の供給に関する行政を所管しています。",
                "citations": [
                    0
                ]
            },
            {
                "text": "民間の経済活力の向上及び対外経済関係の円滑な発展を中心とする経済及び産業の発展並びに鉱物資源及びエネルギー資源の安定的かつ効率的な供給の確保を図るために、マクロ経済政策、産業政策、通商政策、貿易管理業務、産業技術政策、流通政策、エネルギー政策などを所管しています。",
                "citations": [
                    1
                ]
            }
        ]
    },
    "references": [
        {
            "search": {
                "keywords": [
                    "経済産業省"
                ]
            },
            "page": {
                "title": "経済産業省",
                "url": "https://ja.wikipedia.org/wiki/%E7%B5%8C%E6%B8%88%E7%94%A3%E6%A5%AD%E7%9C%81",
                "quote": {
                    "text": "経済産業省（けいざいさんぎょうしょう、英: Ministry of Economy, Trade and Industry、略称: METI）は、日本の行政機関のひとつ[4]。経済および産業の発展ならびに鉱物資源およびエネルギー資源の供給に関する行政を所管する[注釈 1]。"
                }
            }
        },
        {
            "search": {
                "keywords": [
                    "経済産業省"
                ]
            },
            "page": {
                "title": "経済産業省",
                "url": "https://ja.wikipedia.org/wiki/%E7%B5%8C%E6%B8%88%E7%94%A3%E6%A5%AD%E7%9C%81",
                "quote": {
                    "text": "経済産業省設置法第3条の定める任務である「民間の経済活力の向上及び対外経済関係の円滑な発展を中心とする経済及び産業の発展並びに鉱物資源及びエネルギー資源の安定的かつ効率的な供給の確保を図ること」を達成するため、マクロ経済政策、産業政策、通商政策、貿易管理業務、産業技術政策、流通政策、エネルギー政策などを所管する。"
                }
            }
        }
    ]
}
```

## Corresponding author

* Yusuke Oda (@odashi on GitHub)