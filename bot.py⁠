import asyncio
import os
from aiogram import Bot, Dispatcher, types
from aiogram.filters import Command
from aiogram.utils.keyboard import InlineKeyboardBuilder

TOKEN = "8978241804:AAF57y3_A1xiMNedDTuFq9cXbeltc1dcdQA"
ADMIN_ID = 969114895

bot = Bot(token=TOKEN)
dp = Dispatcher()

user_stories = {}

@dp.message(Command("start"))
async def start_handler(message: types.Message):
    if message.from_user.id == ADMIN_ID:
        await message.answer("Це твій адмін-чат. Бот запущено в хмарі!")
        return
    await message.answer("🎵 Привіт! Напиши свою історію, і ми створимо для тебе пісню.")

@dp.message(Command("send"))
async def send_song_to_client(message: types.Message):
    if message.from_user.id != ADMIN_ID:
        return
    
    parts = message.text.split(maxsplit=1)
    if len(parts) < 2:
        await message.answer("⚠️ Вкажи ID! Наприклад: `/send 5033202549`", parse_mode="Markdown")
        return
    
    target_user_id = parts[1].strip()
    audio_file = message.audio or message.voice or message.document
    if not audio_file:
        await message.answer("⚠️ Прикріпи до цього повідомлення аудіофайл і в тексті напиши `/send ID`!")
        return
    
    try:
        await bot.send_audio(
            chat_id=target_user_id,
            audio=audio_file.file_id,
            caption="✨ Твоя унікальна авторська пісня готова! Дякую за довіру 💖"
        )
        await message.answer(f"✅ Пісню успішно надіслано клієнту `{target_user_id}`!", parse_mode="Markdown")
    except Exception as e:
        await message.answer(f"❌ Помилка: {e}")

@dp.message(lambda message: message.photo is not None)
async def photo_handler(message: types.Message):
    user_id = message.from_user.id
    user_name = message.from_user.full_name
    story = user_stories.get(user_id, "Історія не збереглася")
    
    await message.answer("⏳ Скріншот отримано! Адміністратор перевіряє оплату.")
    
    if ADMIN_ID != 0:
        notification_text = (
            "📦 **НОВЕ ЗАМОВЛЕННЯ / ОПЛАТА!**\n\n"
            f"👤 **Клієнт:** {user_name}\n"
            f"🆔 **ID:** `{user_id}`\n\n"
            f"📝 **Історія:**\n{story}\n\n"
            f"👉 *Щоб відправити пісню:* вибери трек у Telegram, у полі підпису напиши `/send {user_id}` і надішли."
        )
        try:
            await bot.send_message(chat_id=ADMIN_ID, text=notification_text, parse_mode="Markdown")
            await bot.forward_message(chat_id=ADMIN_ID, from_chat_id=message.chat.id, message_id=message.message_id)
        except Exception as e:
            print(f"Помилка: {e}")

@dp.message()
async def song_handler(message: types.Message):
    if message.from_user.id == ADMIN_ID:
        return
    
    user_id = message.from_user.id
    user_story = message.text
    user_stories[user_id] = user_story
    
    response_text = (
        f"✨ **Історію прийнято!**\n\n"
        f"📝 *«{user_story}»*\n\n"
        "💰 **Вартість пісні:** `150 грн`\n\n"
        "💳 *Сплати замовлення та надішли скріншот сюди:*\n"
        "🔗 **Банка Monobank:** https://send.monobank.ua/jar/2u9575pEUR\n"
        "🔢 **Картка:** `4874 1000 3297 8747`"
    )
    
    keyboard_builder = InlineKeyboardBuilder()
    keyboard_builder.button(text="💳 Оплатити 150 грн", url="https://send.monobank.ua/jar/2u9575pEUR")
    
    await message.answer(response_text, parse_mode="Markdown", reply_markup=keyboard_builder.as_markup())

async def main():
    await bot.delete_webhook(drop_pending_updates=True)
    print("Бот запущено в хмарі!")
    await dp.start_polling(bot)

if __name__ == "__main__":
    asyncio.run(main())
