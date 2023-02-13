# What are we building?
To test the power of Rust we are going to build a fast data ingestion API which reads data from a Datalake in S3 and stores it into a fast NoSQL database, in our case ScyllaDB. We will use Rust Tokio library to allow asynchronous computing using many threads to speed up the ingestion process.

To complicate a bit the use case and make it more realistic, we will use hierarchical data. We will model graph data using NoSQL so we can run very fast traversals using many threads taking advantage of the power of Rust. In particular, we will see how we can run recursive code using multiple threads, something very dangerous in other languages which Rust makes it very safe.

Finally, we will use the fast Actix Web Framework to create an API to trigger ingestion jobs and also query the data.
[architecture](../../assets/kubectx-demo.gif)

## Database
We will use ScyllaDB that is Cassandra compatible database rewritten in C++ which provides better performance and ultra low latency. It is probably the fastest database on the market. It is open source but some of the features require the enterprise edition. It can run on-prem or in the cloud. The dynamic scheduler provides optimizations for both, OLAP and OLTP queries in the same cluster, making it a great database for all types of queries and tasks.

## Data model
We are going to model hierarchical data in order to showcase Rust and ScyllaDB capabilities when dealing with this type of data. Hierarchical data is complex and traditional SQL databases or other databases such as ElasticSearch do not have great performance. The main reason is that it is hard to write concurrent code when you have a graph because you need to recursion which does not play nicely with multi threading.

Compared with SQL databases, ScyllaDB can run millions of queries in parallel across many CPU parallelizing the workload; these plays nicely with Rust massive parallelization features using Tokio.

You need to think of ScyllaDB more like a storage engine than a full blow database.
ScyllaDB and other NoSQL databases do not provide many features such as joins, but they are extremely good at writing data very fast

We will have data nodes and relations. Each node will have some attributes such name or type and also some tags which is a nested object. We also have relations between the objects.

## Reading Data from S3
```rust
extern crate s3;
use color_eyre::Result;
use s3::bucket::Bucket;
use s3::creds::Credentials;
use s3::Region;
use std::{error::Error, time::Instant};
use url::Url;
use crate::data::source_model::File;
use std::time::Duration;
use tracing::{info, debug};

pub async fn read_file(region: &str, file: String) -> Result<File, Box<dyn Error + Sync + Send>> {
    info!("Reading file: {}", file);
    let now = Instant::now();
    let credentials = Credentials::from_env()?;

    debug!("Credentials: {:?}", credentials);

    let region: Region =  region.parse()?;

    let file_struct: Url = Url::parse(&file)?;

    debug!("file structure: {:?}", file_struct);

    let bucket = file_struct.host_str().unwrap();
    debug!("Bucket {}, Region {:?}", bucket, region);

    let mut bucket = Bucket::new(bucket, region, credentials)?;

    bucket.set_request_timeout(Some(Duration::new(290, 0)));

    let elapsed_b = now.elapsed();
    info!("bucket creation. Took {:.2?}", elapsed_b);
    
    let path = file_struct.path();
    debug!("path: {:?}", path);

    let data = bucket.get_object(path).await?;

    debug!("file code: {:?}", data.status_code());

    let file: File = serde_json::from_slice(&data.bytes())?;

    let elapsed = now.elapsed();
    info!("read_file. Took {:.2?}", elapsed);

    Ok(file)
}
```


[project-ref](https://itnext.io/rust-in-the-real-world-super-fast-data-ingestion-with-scylladb-2111e354b605)