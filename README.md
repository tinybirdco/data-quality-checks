# Implementing data quality checks with Tinybird

This repository includes a simple data project to test for 5 criteria of data quality on the [NYC Taxi dataset](https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page) and publish the results of the tests as an API endpoint that you can use in CI/CD pipelines for data QA.

## Clone the repo
First, clone the repo into a directory of your choice with:

```bash
git clone https://github.com/tinybirdco/data-quality-checks.git
cd data-quality-checks
```

### Set up your .gitignore
Tinybird stores your auth details in a `.tinyb` file. Add this to your `.gitignore` if you intend to push your work to GitHub.

## Setting up Tinybird
If you want to follow along with the examples, go ahead and [sign up](https://www.tinybird.co/signup) for a free Tinybird account and create a Workspace.

Once you've done that, copy your user admin token in the Tinybird UI. It's the token that says `Use it to authenticate with the CLI.`.

### Set up your local environment
The easiest way to install the Tinybird CLI is with pip using a Python virtual environment. Run the following commands:

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install tinybird-cli
```

Then go ahead and set your token as an environment variable for auth:

```bash
export TB_TOKEN=<paste your token here>
```

Authenticate to your Tinybird Workspace with:

```bash
tb auth
```

## Pushing your project to the server
Now you can push the data project files from the repo to the Tinybird server. 

## Create the Data Source
Start by pushing the Data Source with

```bash
tb push datasources/yellow_tripdata.datasource
```

This creates an empty Data Source server side with the correct schema.

### Adding data to the Data Source
Next, add data to the Data Source with

```bash
tb datasource append yellow_tripdata https://d37ci6vzurychx.cloudfront.net/trip-data/yellow_tripdata_2023-03.parquet
```

**Note**: You might find that some rows go to quarantine. As we’ll see, this can be due to data quality issues in the source data! To learn more about data quarantine in Tinybird, [go here](https://www.tinybird.co/docs/concepts/data-sources.html#the-quarantine-data-source). In the meantime, you can ignore this for the sake of following along.

### Push the Pipe
In the `/endpoints` folder is a `.pipe` file. You can push this to the Tinybird server with 

```bash
tb push endpoints/yellow_trip_data_qa_measurements.pipe
```

By default, Tinybird publishes the last node of the Pipe as an API endpoint when you push with the CLI.

### Generate a read token for the Pipe
To create a token to read the API, you'll need to navigate to the Tinybird UI: [https://ui.tinybird.co](https://ui.tinybird.co) for EU or [https://ui.us-east.tinybird.co](https://ui.us-east.tinybird.co) for US-East.

Click "Auth Tokens", then "Add a new token", then "Add Pipe scope", choose `yellow_trip_data_qa_measurements` and give it a `Read` scope.

Copy this token.

## Call your API

You can now call your API and get a JSON result with the following cURL:

```bash
curl --compressed -H 'Authorization: Bearer <TOKEN>'  https://api.tinybird.co/v0/pipes/yellow_trip_data_qa_measurements.json
```

**Note**: For workspaces in US-EAST, use `https://api.us-east.tinybird.co...`

If you navigate back to the Tinybird UI, you can find additional sample usage for the endpoint on the API page.