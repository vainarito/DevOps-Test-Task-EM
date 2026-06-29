FROM python:3.14-alpine

WORKDIR /app

RUN adduser -D -H -h /app -s /bin/sh -g '' appuser

COPY --chown=appuser:appuser app.py /app/

USER appuser

EXPOSE 8080

CMD ["python", "/app/app.py"]
