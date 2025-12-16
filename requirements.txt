from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

BAD_WORDS = ["씨발", "시발", "ㅅㅂ", "병신", "지랄", "좆"]

class KakaoRequest(BaseModel):
    userRequest: dict

@app.post("/skill")
def detect_badword(req: KakaoRequest):
    text = req.userRequest["utterance"].lower()
    detected = any(badword in text for badword in BAD_WORDS)

    if detected:
        return {
            "version": "2.0",
            "template": {
                "outputs": [
                    {
                        "simpleText": {
                            "text": f"⚠️ 욕설 감지됨\n\n{req.userRequest['utterance']}"
                        }
                    }
                ]
            }
        }

    return {
        "version": "2.0",
        "template": {
            "outputs": [
                {"simpleText": {"text": ""}}
            ]
        }
    }
