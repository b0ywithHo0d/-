import streamlit as st
import openai
from regulations import library_regulation  # 도서관 규정 텍스트

st.set_page_config(page_title="부경대 도서관 챗봇", layout="centered")

st.title("📚 국립부경대학교 도서관 챗봇 (GPT 기반)")
st.markdown("도서관 규정에 기반한 질문에 답해드립니다.")

# API 키 입력
openai_api_key = st.secrets.get("openai_api_key") or st.text_input("🔑 OpenAI API 키를 입력하세요", type="password")

# 사용자 질문 입력
user_input = st.text_input("질문을 입력하세요:")

if openai_api_key and user_input:
    openai.api_key = openai_api_key

    system_prompt = f"""
너는 국립부경대학교 도서관의 규정을 잘 알고 있는 도서관 상담 챗봇이야.
아래는 도서관 규정의 일부 내용이야. 이 내용을 바탕으로 사용자 질문에 정확히 답해줘.
답변은 최대한 간결하고 명확하게, 한국어로 해줘.

도서관 규정:
\"\"\"
{library_regulation}
\"\"\"
"""

    with st.spinner("GPT가 답변을 작성 중입니다..."):
        response = openai.ChatCompletion.create(
            model="gpt-4",
            messages=[
                {"role": "system", "content": system_prompt},
                {"role": "user", "content": user_input}
            ],
            temperature=0.3,
        )

        answer = response["choices"][0]["message"]["content"]
        st.success(answer)
