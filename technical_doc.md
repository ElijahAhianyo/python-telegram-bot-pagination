# python-telegram-bot-pagination Technical Documentation

**Last updated:** 2022-01-19\
_Document generation aided by **Documatic**_

<ENTER SHORT PROJECT DESCRIPTION>

* [Introduction](#introduction)
* [Code Overview](#code-overview)

## Introduction

This is a technical document detailing
        at a high-level
        what python-telegram-bot-pagination does, how it operates,
        and how it is built.

The outline of this document was generated
        by **Documatic**.
<!---Documatic-section-group: arch-start--->




## Code Overview

The codebase has a single-depth folder structure, with 10 in total.
<!---Documatic-section-helloworld: setup-start--->

No exact compatable Python version has been detected.
Versions below 3.8 are compatable.

To install, run the command: 

`pip install python-telegram-bot-pagination`


Run `pip install -e .` in top-level directory to install
package in local directory.


#### Usage

```python
        from telegram_bot_pagination import InlineKeyboardPaginator

        paginator = InlineKeyboardPaginator(
            page_count,
            current_page=page,
            data_pattern='page#{page}'
        )

        bot.send_message(
            chat_id,
            text,
            reply_markup=paginator.markup,
        )
```
<!---Documatic-section-helloworld: setup-end--->

<!---Documatic-section-helloworld: entrypoints-start--->


## Entrypoints

There are 0 source code entrypoints in top-level `__main__`/`__init__` files.


<!---Documatic-section-helloworld: entrypoints-end--->

<!---Documatic-section-group: concept-start--->
## Example
<!---Documatic-section-group: concept-end--->
```python
class MyPaginator(InlineKeyboardPaginator):
        first_page_label = '<<'
        previous_page_label = '<'
        current_page_label = '-{}-'
        next_page_label = '>'
        last_page_label = '>>'

    paginator = MyPaginator(page_count)
```
Result:

![](https://github.com/ksinn/python-telegram-bot-pagination/raw/master/examples/media/m2.jpg)


```python
import os

from telebot import TeleBot
from telebot.types import InlineKeyboardButton

from telegram_bot_pagination import InlineKeyboardPaginator
from data import character_pages

bot = TeleBot(os.getenv('BOT_TOKEN'))


@bot.message_handler(func=lambda message: True)
def get_character(message):
    send_character_page(message)


@bot.callback_query_handler(func=lambda call: call.data.split('#')[0]=='character')
def characters_page_callback(call):
    page = int(call.data.split('#')[1])
    bot.delete_message(
        call.message.chat.id,
        call.message.message_id
    )
    send_character_page(call.message, page)


def send_character_page(message, page=1):
    paginator = InlineKeyboardPaginator(
        len(character_pages),
        current_page=page,
        data_pattern='character#{page}'
    )

    paginator.add_before(
        InlineKeyboardButton('Like', callback_data='like#{}'.format(page)),
        InlineKeyboardButton('Dislike', callback_data='dislike#{}'.format(page))
    )
    paginator.add_after(InlineKeyboardButton('Go back', callback_data='back'))

    bot.send_message(
        message.chat.id,
        character_pages[page-1],
        reply_markup=paginator.markup,
        parse_mode='Markdown'
    )


bot.polling()
```

<!---Documatic-section-group: helloworld-end--->

<!---Documatic-section-group: dev-start--->


## Developers
<!---Documatic-section-dev: setup-start--->
* Tests are present in `tests/`




<!---Documatic-section-dev: setup-end--->

<!---Documatic-section-dev: ci-start--->
The project uses Travis for CI/CD.


<!---Documatic-section-dev: ci-end--->

<!---Documatic-section-group: dev-end--->

