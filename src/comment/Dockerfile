# FROM ruby:2.2
# RUN apt-get update -qq && apt-get install -y build-essential

FROM ubuntu:16.04
RUN apt-get update \
 && apt-get install --no-install-recommends -y ruby-full ruby-dev build-essential \
 && gem install bundler --no-ri --no-rdoc \
 && rm -rf /var/libapt/lists/*


ENV APP_HOME /app
RUN mkdir $APP_HOME
WORKDIR $APP_HOME

COPY Gemfile* $APP_HOME/
RUN bundle install
COPY . $APP_HOME

ENV COMMENT_DATABASE_HOST comment_db
ENV COMMENT_DATABASE comments

CMD ["puma"]
