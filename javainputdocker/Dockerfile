FROM openjdk:12-alpine

COPY . /usr/src/myjavaapp

WORKDIR /usr/src/myjavaapp

RUN javac PrimeNumber.java

CMD ["java", "PrimeNumber"]
