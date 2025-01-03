# docs


# Download file from google drive with wget
```bash
!wget --load-cookies /tmp/cookies.txt "https://docs.google.com/uc?export=download&confirm=$(wget --quiet --save-cookies /tmp/cookies.txt --keep-session-cookies --no-check-certificate 'https://docs.google.com/uc?export=download&id=<id_file>' -O- | sed -rn 's/.*confirm=([0-9A-Za-z_]+).*/\1\n/p')&id=<id_file>" -O FILENAME && rm -rf /tmp/cookies.txt
```
# Using docker 
```bash
docker run -d -p 6333:6333 -v D:/NCKH/LLM/Langchain/qdrant-data:/qdrant/storage:z --name qdrant-container --rm qdrant/qdrant
```
