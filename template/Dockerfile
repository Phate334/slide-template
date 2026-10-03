# 官方 Python 3.12 的 Alpine 映像：體積小，而且自帶標準庫 http.server。
# 映像裡只有 ./slide（含離線 vendor），不安裝其他套件，也不連 CDN。
FROM python:3.12-alpine

WORKDIR /srv
COPY slide/ /srv/slide/

EXPOSE 8000

CMD ["python", "-m", "http.server", "8000", "--bind", "0.0.0.0", "--directory", "/srv/slide"]
