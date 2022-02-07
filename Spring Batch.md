# Intro to Spring Batch

Processing of finite amount of data without interaction or interruption.

Dependency: IO > Batch

## Job

List of states/steps and instructions on how to transition from one step to another.

## Step

One piece of processing that makes up the job.

### Tasklet

An interface with just one method `execute()`.

### Chunk

Processing items individually. It contains a reader, a processor, and a writer.
Example: Total items are 1000 and chunk size is 200. 
200 items will be read, processed and written. Then the next chunk is put in the same sequence. Therefore, 5 chunks.

## JobRepository

stores the information of state of different jobs.
There is in-memory job repository and JDBC based job repository. In-memory is used as default but not intented for production work. It is used only for development stage.

Dependency for JDBC Repository: 
`spring-boot-started-jdbc` and `mysql-connector-java`.
MySQL is the data source in this case.

### Job

List of steps. Can be used as many times as required.
*File Reader Job*

### Job Instance

Instance used to represent a job. Lifetime: One complete execution without any failures.
*File Reader Job Instance for July 12*

### Job Execution

Instance used to represent a job attempt. Lifetime: Till failure or completion.
*File Reader Job Attempt for July 12, 9:00 AM - Failed*
*File Reader Job Attempt for July 12, 9:06 AM - Success*

## Batch Flow

Reusable collection of steps and related transitions.

## Batch Split

Used to run multiple flows parallely.

```java
jobBuilderFactory.get(FLOW_NAME)
    .start(flow1())
    .split(new SimpleAsyncTaskExecutor()).add(flow2())
    .end()
    .build();
```

## Batch Decision

Used to decide about future execution using `JobExecutionDecider` which uses the `JobExecution` and previous `StepExecution` (*Nullable*) as parameters for the `decide()` function.

```java
jobBuilderFactory.get("job")
    start(startStep())
    .next(decider())
    .from(decider()).on("ODD").to(oddStep())
    .from(decider()).on("EVEN").to(evenStep())
    .from(oddStep()).on("*").to(decider())
    .from(decider()).on("ODD").to(oddStep())
    .from(decider()).on("EVEN").to(evenStep())
    .end()
    .build();
```

## Batch Nesting

One job can be nested under another job as a `JobStep`. `JobStepBuilder` is used to create a step from a job.

```java
Step childJobStep = new JobStepBuilder(new StepBuilder("childstep"))
    .job(childJob) // Using @Autowired
    .launcher(jobLauncher) // Using @Autowired
    .repository(jobRepository) // As parent job bean's argument
    .transactionManager(transactionManager) // As parent job bean's argument
    .build();
```

For job nesting, we also need to mention to the spring that the child job should not be executed. This is done using `application.properties`.

```properties
spring.batch.job.names=parentJob
```

## Batch Listeners

1. JobExecutionListener
2. StepExecutionListener
3. ChunkListener
   1. ItemReadListener
   2. ItemProcessingListener
   3. ItemWriteListener

## Batch Parameters

Parameters can be passed to the job as:

 ```java
 public Tasklet helloWorld(@Value("#{jobParameters['message']}")  String message){
     String messageToPrint = message;
     return RepeatStatus.FINISHED;
 }
 ```

# Reading Flat Files

## FlatFileItemReader

### Interfaces used

#### Line Mapper

Maps a data line to data object.

#### FieldSetMapper

Creates a data object from tokens. Invoked by `DefaultLineMapper` class.

#### LineTokenizer

Splits a data line into tokens. Invoked by `DefaultLineMapper` class.

#### RecordSeparatorPolicy

Identifies the beginning and end of data records.

#### LineCallbackHandler

Provides data lines in special cases. Common usage is for lines skipped by `FlatFileItemReader`.

### Configuration

#### bufferedReaderFactory

Creates `BufferedReader` instances for the input file.

#### comments

Specifies comment prefixes for the input file.

#### lineMapper

Creates objects from file records.

#### linesToSkip

Number of lines to skip before the beginning of the file.

#### recordSeparatorPolicy

How the input file delimits records. One class can detect single or multiline records.

#### resource

The input resource for the `FlatFileItemReader`.

#### lineCallbackHandler

Callback for lines skipped in the input file. Used jointly with `linesToSkip` property.

#### strict (Boolean)

Whether item reader throws exception if the resource doesn't exist. Default: `true`.

