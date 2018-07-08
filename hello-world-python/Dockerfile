FROM python:3-alpine
WORKDIR /service
COPY requirements.txt .
COPY app.py .
RUN pip install -r requirements.txt
ARG PORT
ENV PORT=${PORT}
EXPOSE ${PORT}
CMD [ "python3", "app.py" ]
