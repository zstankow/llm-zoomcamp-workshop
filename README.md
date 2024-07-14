# RAG and LLM Workshop

In this exercise we will download some documents which are the Q&A of previous zoomcamp sessions. Instead of having to search for the answer to your specific question, you can use elastic search to retrieve several similar questions and their answers. Then, using an LLM (in this case -- OpenAI, for simplicity), we can use the result of elasticsearch as context for the prompt, to provide one answer than takes all the results of elasticsearch into account. In this way we have created a quick way to get your course-related questions answered. 

Basics of RAG:

![image](https://github.com/user-attachments/assets/3524339b-b722-4602-9b87-c968acc16c3d)




## Dependencies
- Python 3.xx
- Docker

```
pip install elasticsearch openai tqdm
```

## Usage

In a bash terminal copy/paste:

```bashrc
docker run -it \
    --rm \
    --name elasticsearch \
    -m 2G \
    -p 9200:9200 \
    -p 9300:9300 \
    -e "discovery.type=single-node" \
    -e "xpack.security.enabled=false" \
    docker.elastic.co/elasticsearch/elasticsearch:8.4.3
```

Verify it is running:
```bashrc
curl http://localhost:9200
```
You should get something like this:
```{
  "name" : "63d0133fc451",
  "cluster_name" : "docker-cluster",
  "cluster_uuid" : "AKW1gxdRTuSH8eLuxbqH6A",
  "version" : {
    "number" : "8.4.3",
    "build_flavor" : "default",
    "build_type" : "docker",
    "build_hash" : "42f05b9372a9a4a470db3b52817899b99a76ee73",
    "build_date" : "2022-10-04T07:17:24.662462378Z",
    "build_snapshot" : false,
    "lucene_version" : "9.3.0",
    "minimum_wire_compatibility_version" : "7.17.0",
    "minimum_index_compatibility_version" : "7.0.0"
  },
  "tagline" : "You Know, for Search"
}
```

Now that the Dockerfile is running, you can insert your OpenAI API key in the line `client = OpenAI(api_key="INSERT API KEY HERE")` and run the notebook `elastic-rag.ipynb`.

## Example usage

### Input
`print(qa_bot("How do I join the course after it has started?"))`

### Output: 

Yes, you can still join the course after it has started. You are eligible to submit the homeworks even if you did not register at the beginning. However, please note that there are deadlines for turning in the final projects, so it's important not to delay too much.

---------------------------------------------------------------------------
### Input

`print(qa_bot("I'm getting invalid reference format: repository name must be lowercase"))`

### Output:

It looks like you're encountering the error "invalid reference format: repository name must be lowercase" when working with Docker, particularly with mounting volumes on Windows. This issue often arises due to differences in handling file paths between different operating systems or formats. Here are some solutions you can try:

1. **Move your data to a folder without spaces:**
   If your project directory contains spaces (e.g., "C:/Users/Alexey Grigorev/git/..."), move it to a location without spaces (e.g., "C:/git/...").

2. **Different volume mapping options:**
   Try replacing the `-v` part of your Docker command with any of the following options:
   ```
   -v /c:/some/path/ny_taxi_postgres_data:/var/lib/postgresql/data
   -v //c:/some/path/ny_taxi_postgres_data:/var/lib/postgresql/data
   -v /c/some/path/ny_taxi_postgres_data:/var/lib/postgresql/data
   -v //c/some/path/ny_taxi_postgres_data:/var/lib/postgresql/data
   --volume //driveletter/path/ny_taxi_postgres_data/:/var/lib/postgresql/data
   ```

3. **Use winpty:**
   Add `winpty` before the entire Docker command:
   ```
   winpty docker run -it \
   -e POSTGRES_USER="root" \
   -e POSTGRES_PASSWORD="root" \
   -e POSTGRES_DB="ny_taxi" \
   -v /c:/some/path/ny_taxi_postgres_data:/var/lib/postgresql/data \
   -p 5432:5432 \
   postgres:1
   ```

4. **Use quotes around the volume path:**
   ```
   -v "/c:/some/path/ny_taxi_postgres_data:/var/lib/postgresql/data"
   -v "//c:/some/path/ny_taxi_postgres_data:/var/lib/postgresql/data"
   -v “/c/some/path/ny_taxi_postgres_data:/var/lib/postgresql/data"
   -v "//c/some/path/ny_taxi_postgres_data:/var/lib/postgresql/data"
   -v "c:\some\path\ny_taxi_postgres_data":/var/lib/postgresql/data
   ```

5. **Use a volume name:**
   If none of the above options work, you can use a named volume instead of specifying the path:
   ```
   -v ny_taxi_postgres_data:/var/lib/postgresql/data
   ```

6. **Use `pwd` variable wrapped with quotes:**
   ```
   -v "$(pwd)/ny_taxi_postgres_data:/var/lib/postgresql/data"
   ```

Try these options and see which one works for your setup. If you continue to face issues, make sure to double-check the syntax and ensure there are no typos...

