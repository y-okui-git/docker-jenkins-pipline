From python:3.11.3-slim
RUN pip install pytest==7.3.1 requests==2.28.2
WORKDIR /src
COPY ./src/test_app.py /src/test_app.py
ENV APP_HOST 127.0.0.1
ENV APP_PORT 80
CMD ["tail", "-f", "/dev/null"]