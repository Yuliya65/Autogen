FROM python:3.15-slim@sha256:ea4f1f290e071bf7d28199cfb3e09f5dbad31b50e9c32fd58f2f2d7be915f3ae
WORKDIR /code

RUN pip install -U gunicorn autogenstudio

# Create a non-root user
RUN useradd -m -u 1000 user
USER user
ENV HOME=/home/user \
    PATH=/home/user/.local/bin:$PATH \
    AUTOGENSTUDIO_APPDIR=/home/user/app

WORKDIR $HOME/app

COPY --chown=user . $HOME/app

CMD gunicorn -w $((2 * $(getconf _NPROCESSORS_ONLN) + 1)) --timeout 12600 -k uvicorn.workers.UvicornWorker autogenstudio.web.app:app --bind "0.0.0.0:8081"
