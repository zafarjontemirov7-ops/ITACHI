from telegram import Update, ReplyKeyboardMarkup, InlineKeyboardButton, InlineKeyboardMarkup
from telegram.ext import ApplicationBuilder, CommandHandler, MessageHandler, CallbackQueryHandler, ContextTypes, filters
import json, random, os, time

TOKEN = "8629180589:AAGJ2euCL8QD0rCGEsD6f9FSubM6UfEg-oo"
ADMIN_ID = 8312091347
CHANNEL_USERNAME = "@itachibull"
DATA_FILE = "users.json"
MIN_WITHDRAW = 350000

menu = [
    ["🧠 Test boshlash"],
    ["💰 Hisobim", "🎁 Bonus"],
    ["👥 Taklif", "📩 Admin"],
    ["💳 Karta", "💸 Pul yechish"]
]

# ===== LOAD =====
try:
    with open(DATA_FILE, "r") as f:
        users = json.load(f)
except:
    users = {}

def save():
    with open(DATA_FILE, "w") as f:
        json.dump(users, f)

# ===== SUB CHECK =====
async def check_sub(bot, user_id):
    try:
        member = await bot.get_chat_member(CHANNEL_USERNAME, user_id)
        return member.status in ["member", "administrator", "creator"]
    except:
        return False

# ===== 50 SAVOL =====
questions = [
{"q":"2x+7=15, x=?", "options":["4","3","5"], "answer":"4"},
{"q":"5*(x+2)=35, x=?", "options":["5","7","3"], "answer":"5"},
{"q":"√196=?", "options":["14","13","12"], "answer":"14"},
{"q":"(3+5)^2=?", "options":["64","56","63"], "answer":"64"},
{"q":"12*6-18=?", "options":["54","72","60"], "answer":"54"},
{"q":"15/3 + 7=?", "options":["12","10","15"], "answer":"12"},
{"q":"x^2=49, x>0, x=?", "options":["7","-7","14"], "answer":"7"},
{"q":"(8+4)/3=?", "options":["4","3","5"], "answer":"4"},
{"q":"3^3 + 2^2=?", "options":["31","29","35"], "answer":"31"},
{"q":"45% of 200=?", "options":["90","80","100"], "answer":"90"},
{"q":"7*8-10=?", "options":["46","48","50"], "answer":"46"},
{"q":"x/5=6, x=?", "options":["30","25","35"], "answer":"30"},
{"q":"2^4 + 3^2=?", "options":["25","16","20"], "answer":"25"},
{"q":"√225 + 10=?", "options":["25","20","15"], "answer":"25"},
{"q":"(6+4)^2 / 5=?", "options":["20","18","22"], "answer":"20"},
{"q":"8*7 - 9=?", "options":["47","46","48"], "answer":"47"},
{"q":"x-7=13, x=?", "options":["20","19","21"], "answer":"20"},
{"q":"5! / 2! = ?", "options":["60","50","40"], "answer":"60"},
{"q":"9*9 + 8=?", "options":["89","81","90"], "answer":"89"},
{"q":"√196 + √49=?", "options":["21","20","19"], "answer":"21"},
{"q":"3*7 + 2^3=?", "options":["29","23","25"], "answer":"29"},
{"q":"50% of 80 + 10=?", "options":["50","40","45"], "answer":"50"},
{"q":"x^2 - 16=0, x>0, x=?", "options":["4","6","8"], "answer":"4"},
{"q":"(15+5)/2 + 3=?", "options":["13","10","11"], "answer":"13"},
{"q":"√64*3=?", "options":["24","20","16"], "answer":"24"},
{"q":"(5*5)-3=?", "options":["22","25","20"], "answer":"22"},
{"q":"18/3 + 7=?", "options":["13","12","10"], "answer":"13"},
{"q":"x*4=28, x=?", "options":["7","6","8"], "answer":"7"},
{"q":"(6+2)^2 / 4=?", "options":["16","12","14"], "answer":"16"},
{"q":"9^2-20=?", "options":["61","60","64"], "answer":"61"},
{"q":"50-2*15=?", "options":["20","25","30"], "answer":"20"},
{"q":"√144 + √36=?", "options":["18","20","16"], "answer":"18"},
{"q":"x/3 + 7=10, x=?", "options":["9","6","8"], "answer":"9"},
{"q":"7*7 - 3=?", "options":["46","48","49"], "answer":"46"},
{"q":"3^3 + 4^2=?", "options":["43","35","36"], "answer":"43"},
{"q":"(12+8)/4=?", "options":["5","4","6"], "answer":"5"},
{"q":"x-8=12, x=?", "options":["20","18","19"], "answer":"20"},
{"q":"5*6 + 10=?", "options":["40","40","40"], "answer":"40"},
{"q":"(10-3)^2=?", "options":["49","50","48"], "answer":"49"},
{"q":"√81 + √16=?", "options":["13","12","14"], "answer":"13"},
{"q":"x/2 + 9 = 15, x=?", "options":["12","10","14"], "answer":"12"},
{"q":"8*5 - 7=?", "options":["33","33","33"], "answer":"33"},
{"q":"(6+4)^2 - 20=?", "options":["80","60","70"], "answer":"80"},
{"q":"x^2 - 36=0, x>0, x=?", "options":["6","8","9"], "answer":"6"},
{"q":"7*6 + 5=?", "options":["47","42","48"], "answer":"47"},
{"q":"√100 + √64=?", "options":["18","14","16"], "answer":"18"},
{"q":"(15-5)*2=?", "options":["20","18","22"], "answer":"20"},
{"q":"x+9=17, x=?", "options":["8","7","9"], "answer":"8"},
{"q":"4*4 + 5*3=?", "options":["31","32","30"], "answer":"31"},
{"q":"√169 + 11=?", "options":["24","23","25"], "answer":"24"}
]

# ===== START =====
async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    user = update.message.from_user
    uid = str(user.id)
    ref = context.args[0] if context.args else None

    if not await check_sub(context.bot, user.id):
        kb = InlineKeyboardMarkup([
            [InlineKeyboardButton("📢 Kanal", url=f"https://t.me/{CHANNEL_USERNAME[1:]}")],
            [InlineKeyboardButton("✅ Tekshirish", callback_data="check")]
        ])
        await update.message.reply_text("❗ Kanalga obuna bo‘ling", reply_markup=kb)
        return

    if uid not in users:
        users[uid] = {"balance":0,"card":None,"ref":0,"joined":time.time(),"last_bonus":0,"ref_by":None}

        if ref and ref != uid and ref in users:
            users[ref]["balance"] += 5000
            users[ref]["ref"] += 1
            users[uid]["ref_by"] = ref

    save()

    text = ("🤖 Xush kelibsiz!\n\n"
            "⏱ 30 daqiqada 12 ta odam taklif qilsangiz\n"
            "💸 3 soatda pul tashlanadi!\n\n")

    await update.message.reply_text(text, reply_markup=ReplyKeyboardMarkup(menu, resize_keyboard=True))

# ===== CALLBACK =====
async def check_callback(update, context):
    query = update.callback_query
    await query.answer()
    if await check_sub(context.bot, query.from_user.id):
        await query.message.reply_text("✅ Tasdiqlandi", reply_markup=ReplyKeyboardMarkup(menu, resize_keyboard=True))

async def answer(update, context):
    query = update.callback_query
    await query.answer()
    uid = str(query.from_user.id)

    q = context.user_data["qs"][context.user_data["i"]]

    if query.data == q["answer"]:
        users[uid]["balance"] += 2000
        context.user_data["score"] += 1
        await query.message.reply_text("✅ +2000")
    else:
        await query.message.reply_text(f"❌ {q['answer']}")

    context.user_data["i"] += 1
    save()
    await send_q(query.message, context)

async def send_q(msg, context):
    i = context.user_data["i"]
    if i >= len(context.user_data["qs"]):
        await msg.reply_text(f"🎉 Natija: {context.user_data['score']}/50")
        return

    q = context.user_data["qs"][i]
    kb = InlineKeyboardMarkup([[InlineKeyboardButton(o, callback_data=o)] for o in q["options"]])
    await msg.reply_text(q["q"], reply_markup=kb)

# ===== MESSAGE =====
async def msg(update, context):
    uid = str(update.message.from_user.id)
    text = update.message.text

    if text == "🧠 Test boshlash":
        context.user_data["qs"] = random.sample(questions, len(questions))
        context.user_data["i"] = 0
        context.user_data["score"] = 0
        await send_q(update.message, context)

    elif text == "💰 Hisobim":
        await update.message.reply_text(f"{users[uid]['balance']} so‘m")

    elif text == "🎁 Bonus":
        if time.time() - users[uid]["last_bonus"] > 259200:
            users[uid]["balance"] += 5000
            users[uid]["last_bonus"] = time.time()
            save()
            await update.message.reply_text("+5000")
        else:
            await update.message.reply_text("❌ Keyinroq")

    elif text == "👥 Taklif":
        link = f"https://t.me/{context.bot.username}?start={uid}"
        await update.message.reply_text(link)

    elif text == "💳 Karta":
        context.user_data["card"] = True
        await update.message.reply_text("Karta kiriting:")

    elif context.user_data.get("card"):
        users[uid]["card"] = text
        context.user_data["card"] = False
        save()
        await update.message.reply_text("✅ Saqlandi")

    elif text == "📩 Admin":
        context.user_data["admin"] = True
        await update.message.reply_text("Yozing:")

    elif context.user_data.get("admin"):
        await context.bot.send_message(ADMIN_ID, f"{uid}: {text}")
        context.user_data["admin"] = False
        await update.message.reply_text("✅ Yuborildi")

    elif text == "💸 Pul yechish":
        if users[uid]["balance"] < MIN_WITHDRAW:
            await update.message.reply_text("❌ 145000 yetarli emas")
            return
        context.user_data["withdraw"] = True
        await update.message.reply_text("Summa kiriting:")

    elif context.user_data.get("withdraw"):
        amount = int(text)
        users[uid]["balance"] -= amount
        save()

        await context.bot.send_message(
            ADMIN_ID,
            f"Yechish:\nUser:{uid}\nSumma:{amount}\nKarta:{users[uid]['card']}"
        )

        context.user_data["withdraw"] = False
        await update.message.reply_text("✅ So‘rov yuborildi")

# ===== RUN =====
app = ApplicationBuilder().token(TOKEN).build()

app.add_handler(CommandHandler("start", start))
app.add_handler(CallbackQueryHandler(check_callback, pattern="check"))
app.add_handler(CallbackQueryHandler(answer))
app.add_handler(MessageHandler(filters.TEXT, msg))

print("Ishlayapti...")
app.run_polling()
