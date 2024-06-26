## Image is built from root repo dir context.
## Example: docker build . -f plugins/sdk/python/example/Dockerfile -t python-scanner

FROM ubuntu:20.04 AS builder-image

# Avoid stuck build due to user prompt
ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update &&  \
    apt-get install --no-install-recommends -y python3.9 python3.9-dev python3.9-venv python3-pip python3-wheel build-essential && \
	  apt-get clean && rm -rf /var/lib/apt/lists/*

# Create and activate virtual environment
# Using final folder name to avoid path issues with packages
RUN python3.9 -m venv /venv
ENV PATH="/venv/bin:$PATH"

# Install SDK from folder
COPY ./plugins/sdk/python /pluginsdk
RUN pip3 install --no-cache-dir wheel
RUN pip3 install --no-cache-dir /pluginsdk


FROM ubuntu:20.04 AS runner-image

RUN apt-get update &&  \
    apt-get install --no-install-recommends -y python3.9 python3-venv && \
	  apt-get clean && rm -rf /var/lib/apt/lists/*

# Copy installed venv packages
COPY --from=builder-image /venv /venv

# Make sure all messages always reach console
ENV PYTHONUNBUFFERED=1

# Activate virtual environment
ENV VIRTUAL_ENV=/venv
ENV PATH="/venv/bin:$PATH"

# Copy example
COPY ./plugins/sdk/python/example ./src

USER 65534

# Run example scanner
CMD ["python3", "/src/scanner.py"]
