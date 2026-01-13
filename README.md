import zipfile

# Номи ZIP файли натиҷа
zip_name = "telegram-math-ai-bot.zip"

# Файлҳои репо
files = {
    "bot.py": """import os
from telegram import Update
from telegram.ext import ApplicationBuilder, MessageHandler, ContextTypes, filters
from openai import OpenAI

TELEGRAM_TOKEN = os.getenv("TELEGRAM_TOKEN")
OPENAI_KEY = os.getenv("OPENAI_KEY")

client = OpenAI(api_key=OPENAI_KEY)

PROMPT = '''
Ту омӯзгори касбии математика ҳастӣ.
ФАҚАТ бо забони тоҷикӣ ҷавоб деҳ.
Ҳамаи мисолҳои математикиро ҳал кун:
ҷамъ, тарҳ, зарб, тақсим, дараҷа, реша, каср, муодила.
Ҷавобро қадам ба қадам шарҳ деҳ.
Агар савол математикӣ набошад, бигӯ:
Ман танҳо мисолҳои математикиро ҳал мекунам.
'''

async def ai_math(update: Update, context: ContextTypes.DEFAULT_TYPE):
    user_text = update.message.text
    try:
        response = client.chat.completions.create(
            model="gpt-4o-mini",
            messages=[
                {"role": "system", "content": PROMPT},
                {"role": "user", "content": user_text}
            ]
        )
        answer = response.choices[0].message.content
    except Exception as e:
        answer = "Хато! Лутфан мисолро дуруст нависед."
    await update.message.reply_text(answer)

app = ApplicationBuilder().token(TELEGRAM_TOKEN).build()
app.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, ai_math))
app.run_polling()
""",

    "requirements.txt": """python-telegram-bot
openai
""",

    "README.md": """# Telegram AI Math Bot (Тоҷикӣ)

Ин бот тавассути AI (OpenAI) мисолҳои математикиро ҳал мекунад ва ҷавобро ба забони **тоҷикӣ** месозад.

## Функсияҳо
- Ҷамъ, тарҳ, зарб, тақсим
- Дараҷа ва реша
- Касрҳо, муодилаҳо
- Шарҳҳои қадам ба қадам

## Deploy (Railway / Replit)
1) Соз кардани repository дар GitHub ба номи `telegram-math-ai-bot`
2) Бор кардани файли `bot.py` ва `requirements.txt`
3) Дар Railway → New Project → Deploy from GitHub
4) Дар Variables:
   TELEGRAM_TOKEN = Токени Telegram бот
   OPENAI_KEY = Калиди API OpenAI
5) Бот онлайн ва фаъол мешавад!
"""
}

# Эҷоди ZIP
with zipfile.ZipFile(zip_name, 'w') as zipf:
    for filename, content in files.items():
        zipf.writestr(filename, content)

print(f"✅ ZIP файли '{zip_name}' бо муваффақият эҷод шуд!")
