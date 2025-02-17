---
title: Similarity Searching with Qdrant
description: Learn how to use the dlt source, Zendesk and dlt destination, Qdrant to conduct a similarity search on your tickets data.
keywords: [similarity search, example]
---
import Header from '../_examples-header.md';

<Header
    intro="In this tutorial, you will learn how to do use dlt to store your
    vectorized Zendesk tickets data in the dlt destination: Qdrant. You can
    use Qdrant's vectorization and similarity searching capabilities on your tickets data,
    while using dlt as a medium to automate your pipeline."
    slug="qdrant_zendesk"
    run_file="qdrant"
    destination="qdrant"
/>

This article outlines a system to map vectorized ticket data from Zendesk to Qdrant, similar to our guide on the topic concerning [Weaviate](https://dlthub.com/docs/dlt-ecosystem/destinations/qdrant). In this example, we will:
- Connect to our [Zendesk source](https://dlthub.com/docs/dlt-ecosystem/verified-sources/zendesk).
- Extract tickets data from our Zendesk source.
- [Create a dlt pipeline](https://dlthub.com/docs/walkthroughs/create-a-pipeline) with Qdrant as destination.
- Vectorize/embed the tickets data from Zendesk.
- Pass the vectorized data to be stored in Qdrant via the dlt pipeline.
- Query data that we stored in Qdrant.
- Explore the similarity search results.

First, configure the destination credentials for [Qdrant](https://dlthub.com/docs/dlt-ecosystem/destinations/qdrant#setup-guide) and [Zendesk](https://dlthub.com/docs/walkthroughs/zendesk-weaviate#configuration) in `.dlt/secrets.toml`.

Next, make sure you have the following dependencies installed:

```sh
pip install qdrant-client>=1.6.9
pip install fastembed>=0.1.1
```

## Connect to Zendesk and load tickets data

<!--@@@DLT_SNIPPET ./code/qdrant-snippets.py::zendesk_conn-->


## Inititating a pipeline with Qdrant
<!--@@@DLT_SNIPPET ./code/qdrant-snippets.py::main_code-->

## Querying the data

<!--@@@DLT_SNIPPET ./code/qdrant-snippets.py::declare_qdrant_client-->


<!--@@@DLT_SNIPPET ./code/qdrant-snippets.py::get_response-->


The query above gives stores the following results in the `response` variable:
```py
[QueryResponse(id='6aeacd21-b3d0-5174-97ef-5aaa59486414', embedding=None, metadata={'_dlt_id': 'Nx3wBiL29xTgaQ', '_dlt_load_id': '1700130284.002391', 'allow_attachments': True, 'allow_channelback': False, 'assignee_id': 12765072569105, 'brand_id': 12765073054225, 'created_at': '2023-09-01T11:19:25+00:00', 'custom_status_id': 12765028278545, 'description': 'I have been trying to cancel my subscription but the system won’t let me do it. Can you please help?', 'from_messaging_channel': False, 'generated_timestamp': 1693567167, 'group_id': 12765036328465, 'has_incidents': False, 'id': 12, 'is_public': True, 'organization_id': 12765041119505, 'raw_subject': 'Unable to Cancel Subscription', 'requester_id': 12765072569105, 'status': 'open', 'subject': 'Unable to Cancel Subscription', 'submitter_id': 12765072569105, 'tags': ['test1'], 'test_field': 'test1', 'ticket_form_id': 12765054772497, 'updated_at': '2023-09-01T11:19:25+00:00', 'url': 'https://d3v-dlthub.zendesk.com/api/v2/tickets/12.json', 'via__channel': 'web'}, document='', score=0.89545774),
 QueryResponse(id='a22189c1-70ab-5421-938b-1caae3e7d6d8', embedding=None, metadata={'_dlt_id': 'bc/xloksL89EUg', '_dlt_load_id': '1700130284.002391', 'allow_attachments': True, 'allow_channelback': False, 'assignee_id': 12765072569105, 'brand_id': 12765073054225, 'created_at': '2023-07-18T17:23:42+00:00', 'custom_status_id': 12765028278545, 'description': 'ABCDEF', 'from_messaging_channel': False, 'generated_timestamp': 1689701023, 'group_id': 12765036328465, 'has_incidents': False, 'id': 4, 'is_public': True, 'organization_id': 12765041119505, 'raw_subject': 'What is this ticket', 'requester_id': 12765072569105, 'status': 'open', 'subject': 'What is this ticket', 'submitter_id': 12765072569105, 'tags': ['test1'], 'test_field': 'test1', 'ticket_form_id': 12765054772497, 'updated_at': '2023-07-18T17:23:42+00:00', 'url': 'https://d3v-dlthub.zendesk.com/api/v2/tickets/4.json', 'via__channel': 'web'}, document='', score=0.8643349),
 QueryResponse(id='ce2f1c5c-41c3-56c3-a31d-2399a7a9239d', embedding=None, metadata={'_dlt_id': 'ZMuFJZo0AJxV4A', '_dlt_load_id': '1700130284.002391', 'allow_attachments': True, 'allow_channelback': False, 'assignee_id': 12765072569105, 'brand_id': 12765073054225, 'created_at': '2023-03-14T10:52:28+00:00', 'custom_status_id': 12765028278545, 'description': 'X', 'from_messaging_channel': False, 'generated_timestamp': 1696163084, 'group_id': 12765036328465, 'has_incidents': False, 'id': 2, 'is_public': True, 'priority': 'high', 'raw_subject': 'SCRUBBED', 'requester_id': 13726460510097, 'status': 'deleted', 'subject': 'SCRUBBED', 'submitter_id': 12765072569105, 'tags': [], 'ticket_form_id': 13726337882769, 'type': 'question', 'updated_at': '2023-09-01T12:10:35+00:00', 'url': 'https://d3v-dlthub.zendesk.com/api/v2/tickets/2.json', 'via__channel': 'web'}, document='', score=0.8467072)]
```
To get a closer look at what the Zendesk ticket was, and how dlt dealt with it, we can index into the metadata of the first `QueryResponse` object:
```py
{'_dlt_id': 'Nx3wBiL29xTgaQ',
 '_dlt_load_id': '1700130284.002391',
 'allow_attachments': True,
 'allow_channelback': False,
 'assignee_id': 12765072569105,
 'brand_id': 12765073054225,
 'created_at': '2023-09-01T11:19:25+00:00',
 'custom_status_id': 12765028278545,
 'description': 'I have been trying to cancel my subscription but the system won’t let me do it. Can you please help?',
 'from_messaging_channel': False,
 'generated_timestamp': 1693567167,
 'group_id': 12765036328465,
 'has_incidents': False,
 'id': 12,
 'is_public': True,
 'organization_id': 12765041119505,
 'raw_subject': 'Unable to Cancel Subscription',
 'requester_id': 12765072569105,
 'status': 'open',
 'subject': 'Unable to Cancel Subscription',
 'submitter_id': 12765072569105,
 'tags': ['test1'],
 'test_field': 'test1',
 'ticket_form_id': 12765054772497,
 'updated_at': '2023-09-01T11:19:25+00:00',
 'url': 'https://d3v-dlthub.zendesk.com/api/v2/tickets/12.json',
 'via__channel': 'web'}
```