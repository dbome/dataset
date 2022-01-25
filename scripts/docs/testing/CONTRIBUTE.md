# Contribute - Test data
We welcome contributions to our testing dataset! 

Automated countries can be found under [automations](../../scripts/testing/automations) folder. Some countries have a _batch_ process while
others an _incremental_ one.

- **batch**: Complete timeseries is updated in every execution. This process is prefered, as it means the source can correct past data.
- **incremental**: Only last data point is added. 

The code consists of a mixture of python and R scripts. We currently only accept contributions written in python.

### Content
- [Add a new country](#add-a-new-country)
    - [Steps to contribute](#steps-to-contribute)
- [Criteria to accept pull requests](#criteria-to-accept-pull-requests)
- [Metrics collected](#metrics-collected)
    - [Prioritisation of metrics](#prioritisation-of-metrics)

## Add a new country
To automate the data import process for a country, make sure that:
- The source is reliable.
- The source provides data in a format that can be easily read:
    - As a file (e.g. csv, json, xls, etc.)
    - As plain text in source HTML, which can be easily scraped.

### Steps to contribute
1. Decide if the import is batch (i.e. all the timeseries) or incremental (last value). See the scripts in
   [`automations/batch`](../../scripts/testing/automations/batch) and [`automations/incremental`](../../scripts/testing/automations/incremental) for more details. **Note: Batch is prefered over Incremental**.
2. Create a script and place it based on decision in step 1 either in [`automations/batch`](../../scripts/testing/automations/batch) or
   [`automations/incremental`](../../scripts/testing/automations/incremental). Note that each source is different and there is no single pattern that works for all sources, however you can take some inspiration from the scripts below:
    - Batch imports:
        - HTML table: [Turkey](../../scripts/testing/automations/batch/turkey.py)
        - HTML elements: [Slovenia](../../scripts/testing/automations/batch/turkey.py)
        - API/JSON: [Portugal](../../scripts/testing/automations/batch/portugal.py)
        - CSV: [France](../../scripts/testing/automations/batch/france.py)
    - Incremental imports:
        - CSV: [Equatorial Guinea](../../scripts/testing/automations/incremental/equatorial-guinea.py)
        - HTML elements: [Belize](../../scripts/testing/automations/incremental/belize.py)
3. Make sure that you are collecting the right metrics (for more details, read section [Metrics collected](#metrics-collected)).
4. Test that the script works and is stable.
5. Create a pull request with your code!


## Criteria to accept pull requests
- Limit your pull request to a single country or a single feature.
- We welcome code improvements / bug fixes. As an example, you can take [#465](https://github.com/owid/covid-19-data/pull/465).

You can of course, and we appreciate it very much, create pull requests for other cases.

Note that files in [public folder](https://github.com/owid/covid-19-data/tree/master/public) are not to be modified via
Pull requests.

## Metrics collected
For each country we collect metadata variables such as:

- `Country`: Name of the country or territory
- `Date`: Date of the reported data
- `Units`: Units of the reported data. This can be one of `people tested`, `tests performed` and `samples tested`. 
    - `people tested`: Number of persons tested.
    - `tests performed`: Number of tests performed. Note that a single person can be tested more than once.
    - `samples tested`: Number of samples tested. Note that in some cases, more than one sample may be require to
      perform a test.
- `Source URL`: URL to the source.
- `Source label`: Name of the source.
- `Notes`: Additional notes (optional).

In addition, we may collect one or all of the following two metrics:

- `Cumulative total`: Cumulative number of people tested, tests performed or samples tested (dependant on `Units`).
- `Daily change in cumulative total`: Daily number of new people tested, tests performed or samples tested (dependand
  on `Units`).

To better understand which metric is preferred in each case, please read the [following section](#how-do-we-prioritize-test-metrics).

Finally, if we deem it appropriate, we also estimate the positive rate (`Positive rate`). This is done whenever we
consider that the data provided by JHU on confirmed cases might be inaccurate.

### Prioritisation of metrics

#### `Cumulative total` vs `Daily change in cumulative total`
1. The ideal situation is to collect `Cumulative total`. From it, we can infer intermediate totals (with a linear
   forward-fill) and create the 7-day average daily series. So in this situation, our dataset will have Cumulative total
   and 7-day, **which is perfect**.
    
    - Examples: [`greece.py`](../../scripts/testing/automations/incremental/greece.py), [`italy.py`](../../scripts/testing/automations/batch/italy.py), [`brazil.py`](../../scripts/testing/automations/incremental/brazil.py)
2. The second-best situation, if there is no `Cumulative total`, is to collect `Daily change in cumulative total` every
   day instead. If the daily number is _really_ present each day, then our script will calculate the 7-day average.

   - Examples: [`chile.py`](../../scripts/testing/automations/batch/chile.py), [`albania.py`](../../scripts/testing/automations/incremental/albania.py)
3. The "worst" situation is to have an irregular series of Daily change in cumulative total. This is basically of little
   use, because we can't calculate any cumulative total (because some days are missing) and we also can't calculate the
   7-day average (because some days are missing).
   
   - Examples: [`moldova.py`](../../scripts/testing/automations/incremental/moldova.py)

#### `Positive rate`
Examples: [`argentina.py`](../../scripts/testing/automations/incremental/argentina.py), [`france.py`](../../scripts/testing/automations/incremental/france.py)
