From python:3.11.3-slim
RUN pip install pytest==7.3.1 requests==2.28.2 selenium==4.8.2
RUN mkdir /images
WORKDIR /src
COPY ./src/test_static.py /src/test_static.py
COPY ./src/test_selenium.py /src/test_selenium.py
ENV SELENIUM_HUB_HOST 127.0.0.1
ENV SELENIUM_HUB_PORT 4444
ENV WEB_HOST 127.0.0.1
ENV WEB_PORT 80
CMD ["tail", "-f", "/dev/null"]