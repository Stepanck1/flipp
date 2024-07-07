import random
import telegram
from telegram.ext import Updater, CommandHandler, MessageHandler, Filters, ConversationHandler
from telegram import InlineKeyboardButton, InlineKeyboardMarkup

TOKEN = 'YOUR_BOT_TOKEN'  # Замените на ваш токен бота

# Состояния беседы
START, BOARD, FLIP_UP = range(3)

# Данные пользователя
user_data = {}

def start(update, context):
    user_id = update.message.from_user.id
    user_data[user_id] = {'score': 0, 'flip_bonus': 1}
    update.message.reply_text(
        "Добро пожаловать в West Coast Board! Нажми на кнопку Flip, чтобы начать!",
        reply_markup=get_main_keyboard()
    )
    return BOARD

def flip(update, context):
    user_id = update.message.from_user.id
    user_data[user_id]['score'] += user_data[user_id]['flip_bonus']
    update.message.reply_text(
        f"Ты сделал флип! Твой счет: {user_data[user_id]['score']}",
        reply_markup=get_main_keyboard()
    )

def flip_up(update, context):
    update.message.reply_text(
        "Выбери, куда ты хочешь прокачать свой Flip:",
        reply_markup=get_flip_up_keyboard()
    )
    return FLIP_UP

def board(update, context):
    user_id = update.message.from_user.id
    update.message.reply_text(
        f"Ты вернулся на Board! Твой счет: {user_data[user_id]['score']}",
        reply_markup=get_main_keyboard()
    )
    return BOARD

def board_up(update, context):
    user_id = update.message.from_user.id
    user_data[user_id]['score'] -= 150
    user_data[user_id]['flip_bonus'] += 2
    update.message.reply_text(
        f"Ты прокачал свой Flip! Твой счет: {user_data[user_id]['score']}n"
        f"Бонус к флипам: +{user_data[user_id]['flip_bonus']}",
        reply_markup=get_main_keyboard()
    )
    return BOARD

def skate_park_up(update, context):
    user_id = update.message.from_user.id
    user_data[user_id]['score'] -= 300
    user_data[user_id]['flip_bonus'] += 3
    update.message.reply_text(
        f"Ты прокачал свой Flip! Твой счет: {user_data[user_id]['score']}n"
        f"Бонус к флипам: +{user_data[user_id]['flip_bonus']}",
        reply_markup=get_main_keyboard()
    )
    return BOARD

def west_coast_up(update, context):
    user_id = update.message.from_user.id
    user_data[user_id]['score'] -= 500
    user_data[user_id]['flip_bonus'] += 4
    update.message.reply_text(
        f"Ты прокачал свой Flip! Твой счет: {user_data[user_id]['score']}n"
        f"Бонус к флипам: +{user_data[user_id]['flip_bonus']}",
          reply_markup=get_main_keyboard()
    )
    return BOARD

def get_main_keyboard():
    keyboard = [
        [InlineKeyboardButton("Flip", callback_data='flip')],
        [
            InlineKeyboardButton("Board", callback_data='board'),
            InlineKeyboardButton("Flip Up", callback_data='flip_up'),
        ],
        [InlineKeyboardButton("West Coast UP", callback_data='west_coast_up')],
    ]
    return InlineKeyboardMarkup(keyboard)

def get_flip_up_keyboard():
    keyboard = [
        [InlineKeyboardButton("Board", callback_data='board_up')],
        [InlineKeyboardButton("Skate Park", callback_data='skate_park_up')],
        [InlineKeyboardButton("West Coast UP", callback_data='west_coast_up')],
    ]
    return InlineKeyboardMarkup(keyboard)

def handle_callback_query(update, context):
    query = update.callback_query
    user_id = query.from_user.id

    if query.data == 'flip':
        flip(update, context)
    elif query.data == 'board':
        board(update, context)
    elif query.data == 'flip_up':
        flip_up(update, context)
    elif query.data == 'board_up':
        board_up(update, context)
    elif query.data == 'skate_park_up':
        skate_park_up(update, context)
    elif query.data == 'west_coast_up':
        west_coast_up(update, context)

    query.answer()

def main():
    updater = Updater(TOKEN, use_context=True)
    dp = updater.dispatcher

    # Обработка команд
    dp.add_handler(CommandHandler("start", start))

    # Обработка кнопок
    dp.add_handler(CallbackQueryHandler(handle_callback_query))

    # Запуск бота
    updater.start_polling()
    updater.idle()

if __name__ == '__main__':
    main()
