# Python bitwise operator

아래 코드 이해가 힘들어서 찾아봄.

bitwise operator가 아니라 apache beam library에서 사용하는 구문으로 보임...

```python
(p
     | 'Read From Text' >> beam.io.ReadFromText(known_args.input,
                                                skip_header_lines=1)
     | 'String to BigQuery Row' >> beam.Map(lambda s:
                                            data_ingestion.parse_method(s))
     | 'Join Data' >> beam.Map(add_full_state_name, AsDict(
        state_abbreviations))
     | 'Write to BigQuery' >> beam.io.Write(
        beam.io.BigQuerySink(
            known_args.output,
            schema=schema,
            create_disposition=beam.io.BigQueryDisposition.CREATE_IF_NEEDED,
            write_disposition=beam.io.BigQueryDisposition.WRITE_TRUNCATE)))
```

## Bit
