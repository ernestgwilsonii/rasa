# REF: http://rasa.com/products/rasa-stack/
# and https://hub.docker.com/r/rasa/rasa_nlu/
# and https://github.com/RasaHQ/rasa_nlu
# and https://nlu.rasa.com
# Create training data web interface: https://rasahq.github.io/rasa-nlu-trainer/


# Create the bind mount directories and copy files to the correct node based on constraints in the docker-compose!
mkdir -p /opt/rasa/data
mkdir -p /opt/rasa/logs
mkdir -p /opt/rasa/projects
chmod -R a+rw /opt/rasa

# Run on the Swarm
docker stack deploy -c docker-compose.yml rasa
#docker stack rm rasa

# Verify Docker
docker service ls | grep rasa
docker service logs -f rasa_rasa

# Quick verify app
curl 'http://localhost:5000/status'
curl 'http://localhost:5000/version'
curl 'http://localhost:5000/parse?q=hello'


# Next: Train RASA
# https://github.com/RasaHQ/rasa_nlu/tree/master/data/examples/rasa
# https://nlu.rasa.com/dataformat.html
#
# Train a new project (takes a few minutes or more)
curl -X POST -d "@./example-training-data.json" "localhost:5000/train?project=rasaTutorialBot"
#
# Test out your new model (the very first test may take a bit, faster after that)
# High confidence
curl --request POST \
  --url http://localhost:5000/parse \
  --header 'content-type: application/json' \
  --data '{ "q": "I want to hear a chuck norris joke about school", "project": "rasaTutorialBot" }'
#
# Less confidence (since "bob" is unknown)
curl --request POST \
  --url http://localhost:5000/parse \
  --header 'content-type: application/json' \
  --data '{ "q": "I want to hear a bob norris joke about school", "project": "rasaTutorialBot" }'


# Further Reading:
##################
#
# ChatBot Tutorial
# Build A Chatbot with Rasa NLU — Part One
#    REF: https://blog.spg.ai/build-a-chatbot-with-rasa-nlu-dc2bfb55edb2
# Build a Chatbot with Rasa NLU — Part Two
#    REF: https://blog.spg.ai/build-a-chatbot-with-rasa-nlu-part-two-8d533a0cfda8
# Build a Chatbot with Rasa NLU — Part Three
#    REF: https://blog.spg.ai/build-a-chatbot-was-rasa-nlu-part-3-b53c61954e86
#
# RASA NLU Videos
# RASA NLU Tutorial - 1 - Rasa nlu setup, installation, configuration, train first model
#   REF: https://www.youtube.com/watch?v=fz59KCxE1fQ
# RASA NLU Tutorial - 2 - Setup/Install Rasa NLU Trainer UI/GUI on Windows
#   REF: https://www.youtube.com/watch?v=8S6e7xo_zsE
# RASA NLU Tutorial - 3 - Setup/Install Rasa NLU Trainer UI/GUI on Linux
#   REF: https://www.youtube.com/watch?v=S7GfG61TwDQ&t=529s
 
