FROM python:3.9

RUN apt-get install wget
RUN pip install pandas sqlalchemist psycopg2

WORKDIR /app
COPY ingest_data.py pipeline.py
ENTRYPOINT [ "python","pipeline.py" ]