# Performance Monitoring


## Skylight
- [Skylight's own guide](https://www.skylight.io/support/skylight-guides) is the best introduction.
- `Liefery Production` has two views in Skylight - one for endpoints and one for background jobs.
- When you view a particular endpoint or background job on Skylight, you are looking at a sampling of requests made to that endpoint in the given timeframe. It is only a *sample* of the requests made in that time - there's no guarantee that the one you're interested in appears there - and Skylight cannot be used to analyse the content of specific requests (eg, which parameters caused a slow response).
- The `segment` field displays the range of response times for the given endpoint in the given time period. A `segment` value of `434ms-1.4s` and a `requests` value of `22` means that there were 22 requests made to the given endpoint in the given time period, where the slowest was 1.4s and the fastest 434ms. Again - this only describes the sampled data, and not every request to that endpoint in the given time period.
- Skylight has a useful blog on [improving performance](https://www.skylight.io/support/performance-tips) in Rails applications, including [how to fix a mysteriously slow request](https://blog.skylight.io/fixing-the-mysterious-slow-request/).

## Kibana

### General information
- If you want to filter our visualisations by a specific endpoint, do so in the "Endpoint Trends" dashboard. Don't try to filter by a specific endpoint in the main Production - IT Dashboard dashboard. ElasticSearch can freak out because many of the dashboard graphs don't make sense when filtered that way, and our Kibana logs will be inaccessible for anyone company-wide for ~15 minutes.

### Understanding the metrics
- **load**: the number of processes waiting to be executed. Our production servers have four cores, so a load of greater than 4 is bad: it means we have more processes than our servers can handle.

### Understanding the logs
- If you want to compare a log in `prooduction-rails` with its counterpart in `production-access_log` (in order to see the params passed in the URL), the logs in `production-access_log` are usually timestamped slightly later (eg 2-30 seconds later) than their equivalent in `production-rails`. The only shared fields to help confirm you're looking at the same log are which server it went to (101 or 102) and comparing the `request` field in `production-access_log` with the `controller_and_action` field in `production-rails`.

## So you're investigating a long database request time
- The most likely explanation is a database lock.
    - Find the relevant endpoint request in Skylight, and explore the SQL statements to see if rows are being inserted or updated (because those actions use locks).
- Try to recreate the exact request locally.
    - For GET requests only: the `production-access-log` in Kibana - unlike the `production-rails` log - stores the parameters as part of the URL. You can copy and paste these to recreate the specific parameters that caused the long db response time.
    - You can also directly message the person who made the request and ask them what they did.

## Unsolved Mysteries
- **Very slow index actions**: `Admin::TourShipmentsController#index` and `Admin::ToursController#index` are some of our worst-performing endpoints.
    - Their average response time is not that slow, but individual responses regularly (ie, ~2x a day) take longer than 20 seconds. This is confusing because they are only index actions (and so should not be affected by any db locks).
    - ToursController#index can be this slow with or without search parameters. It also doesn't seem to be caused by unlucky timing - there are no existing slow db processes around the same time. So we presume it's internal to the controller action. But why only occasionally?
    - This is mysterious, but it's not necessarily high-priority because the average response time for the endpoint is fine and the problem response time is rare.
    
    
## Metrics to judge improvements
- **TourShipment#index** 
    - Are the longer-than-20s responses just as frequent post-fix? Does Skylight Trends show any improvement? Does the endpoint breakdown in Skylight show the same long TourShipment allocation bar?
