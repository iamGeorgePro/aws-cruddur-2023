# Stage 1: Build the application
FROM 424925313933.dkr.ecr.us-east-1.amazonaws.com/cruddur-python:3.10-slim-buster 

# Inside Container
# make a new folder inside container
WORKDIR /backend-flask

# Outside Container -> Inside Container
# this contains the libraries want to install to run the app
COPY requirements.txt requirements.txt

# Inside Container
# Install the python libraries used for the app
RUN pip3 install -r requirements.txt

# Outside Container -> Inside Container
# . means everything in the current directory
# first period . - /backend-flask (outside container)
# second period . /backend-flask (inside container)
COPY . .

EXPOSE ${PORT}

# Environment Variable
#ENV FLASK_APP=app.py

# CMD (Command)
# python3 -m flask run --host=0.0.0.0 --port=4567
CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0", "--port=4567", "--debug"]


# WORKDIR /backend-flask

# COPY requirements.txt requirements.txt
# RUN pip3 install --user --no-cache-dir -r requirements.txt

# COPY . .

# # Stage 2: Run the application
# FROM 424925313933.dkr.ecr.us-east-1.amazonaws.com/cruddur-python

# WORKDIR /backend-flask

# COPY --from=builder /root/.local /root/.local
# ENV PATH=/root/.local/bin:$PATH

# RUN apt-get update && apt-get install -y --no-install-recommends \
#     gcc \
#     curl \
#     && rm -rf /var/lib/apt/lists/*

# COPY flask.sh /usr/local/bin
# RUN chmod +x /usr/local/bin/flask.sh

# ENV FLASK_ENV=development
# ENV PORT=4567

# EXPOSE ${PORT}

# CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0", "--port=4567"]
