# Stage 1: Build the application
FROM 424925313933.dkr.ecr.us-east-1.amazonaws.com/cruddur-python

WORKDIR /backend-flask

COPY requirements.txt requirements.txt
RUN pip3 install --user --no-cache-dir -r requirements.txt

COPY . .

# Stage 2: Run the application
FROM 424925313933.dkr.ecr.us-east-1.amazonaws.com/cruddur-python

WORKDIR /backend-flask

COPY --from=builder /root/.local /root/.local
ENV PATH=/root/.local/bin:$PATH

RUN apt-get update && apt-get install -y --no-install-recommends \
    gcc \
    curl \
    && rm -rf /var/lib/apt/lists/*

COPY flask.sh /usr/local/bin
RUN chmod +x /usr/local/bin/flask.sh

ENV FLASK_ENV=development
ENV PORT=4567

EXPOSE ${PORT}

CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0", "--port=4567"]
