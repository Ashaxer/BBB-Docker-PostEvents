FROM alangecker/bbb-docker-recordings:v3.0.4

# install ruby dependencies
RUN cd /usr/local/bigbluebutton/core \
    && gem install builder \
    && gem install redis \
    && gem install builder \
    && gem install bbbevents \
    && gem install fileutils \
    && gem install yaml \
    && gem install nokogiri \
    && gem install loofah \
    && gem install open4 \
    && gem install absolute_time \
    && gem install journald-logger \
    && gem install resque \
    && gem install jwt \
    && gem install java_properties \
    && gem install bbbevents \
    && gem install bundler --no-document \
    && bundle config set --local deployment true \
    && bundle install \
    && bundle clean