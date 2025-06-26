import os
import re
import json
import requests
from datetime import datetime
from typing import Dict, List, Optional
import pytz

# Конфигурация (заменить на реальные ключи)
TELEGRAM_TOKEN = "YOUR_TELEGRAM_BOT_TOKEN"
OPENAI_API_KEY = "YOUR_OPENAI_API_KEY"
GITHUB_TOKEN = "YOUR_GITHUB_TOKEN"

class CodeMasterBot:
    def __init__(self):
        self.commands = {
            "/start": "Запуск бота",
            "/help": "Все команды",
            "/code <вопрос>": "Генерация/анализ кода",
            "/doc <язык> <запрос>": "Поиск в документации",
            "/convert <исходный> <целевой>": "Конвертация кода",
            "/explain": "Объяснение выделенного кода",
            "/bug": "Поиск ошибок в коде",
            "/git <команда>": "Работа с GitHub",
            "/remind <время> <текст>": "Установка напоминания"
        }
        self.reminders = {}
    
    def handle_message(self, message: str, user_id: int) -> str:
        """Обработка входящих сообщений"""
        message = message.strip()
        
        if message.startswith("/start"):
            return self._welcome_message()
        
        elif message.startswith("/help"):
            return self._show_help()
        
        elif message.startswith("/code"):
            return self._generate_code(message[5:].strip())
        
        elif message.startswith("/doc"):
            return self._search_docs(message[4:].strip())
        
        elif message.startswith("/convert"):
            return self._convert_code(message[8:].strip())
        
        elif message.startswith("/explain"):
            return "Отправьте фрагмент кода для объяснения"
        
        elif message.startswith("/bug"):
            return "Отправьте код для поиска ошибок"
        
        elif message.startswith("/git"):
            return self._handle_git(message[4:].strip())
        
        elif message.startswith("/remind"):
            return self._set_reminder(message[7:].strip(), user_id)
        
        elif "bug" in message.lower() or "ошибка" in message.lower():
            return self._find_bugs(message)
        
        elif "объясни" in message.lower() or "explain" in message.lower():
            return self._explain_code(message)
        
        else:
            return self._general_response(message)
    
    def _welcome_message(self) -> str:
        return ("🚀 Привет, программист! Я CodeMaster Assistant 2025\n"
                "Мои возможности:\n"
                "- Генерация кода на 50+ языках\n"
                - Анализ и поиск ошибок\n"
                - Конвертация между языками\n"
                - Работа с GitHub\n"
                - Поиск в документациях\n"
                - Умные напоминания\n\n"
                "Напиши /help для списка команд")
    
    def _show_help(self) -> str:
        return "\n".join([f"{cmd} - {desc}" for cmd, desc in self.commands.items()])
    
    def _generate_code(self, prompt: str) -> str:
        """Генерация кода через OpenAI API"""
        try:
            response = requests.post(
                "https://api.openai.com/v1/chat/completions",
                headers={"Authorization": f"Bearer {OPENAI_API_KEY}"},
                json={
                    "model": "gpt-5-turbo",
                    "messages": [{"role": "user", "content": f"Сгенерируй код: {prompt}"}],
                    "max_tokens": 1000
                }
            )
            result = response.json()
            code = result['choices'][0]['message']['content']
            return f"```\n{code}\n```"
        except Exception as e:
            return f"Ошибка генерации: {str(e)}"
    
    def _search_docs(self, query: str) -> str:
        """Поиск в документации (упрощенная реализация)"""
        # В реальной версии здесь интеграция с официальными API документаций
        langs = ["python", "javascript", "java", "c++", "rust", "go"]
        lang_match = re.search(r"(\w+)\s(.+)", query)
        
        if lang_match:
            lang, term = lang_match.groups()
            if lang.lower() in langs:
                return (f"📚 Документация {lang.upper()} по '{term}':\n"
                        f"https://{lang}.org/docs/{term.replace(' ', '_')}")
        return "Используйте: /doc <язык> <запрос>"
    
    def _convert_code(self, params: str) -> str:
        """Конвертация кода между языками"""
        # В реальной версии используется AST-анализ
        match = re.match(r"(\w+)\s+(\w+)\s+(.+)", params, re.DOTALL)
        if not match:
            return "Неверный формат. Используйте: /convert <исходный_язык> <целевой_язык> <код>"
        
        src_lang, tgt_lang, code = match.groups()
        return f"Конвертация {src_lang} → {tgt_lang}:\n```{tgt_lang}\n// Реализация конвертации\n```"
    
    def _explain_code(self, code: str) -> str:
        """Объяснение кода с помощью AI"""
        return "🤖 Анализ кода:\n" + self._call_ai_api(f"Объясни этот код:\n{code}")
    
    def _find_bugs(self, code: str) -> str:
        """Поиск ошибок в коде"""
        return "🐛 Отчет об ошибках:\n" + self._call_ai_api(f"Найди ошибки в коде:\n{code}")
    
    def _call_ai_api(self, prompt: str) -> str:
        """Общий вызов AI API"""
        try:
            response = requests.post(
                "https://api.openai.com/v1/chat/completions",
                headers={"Authorization": f"Bearer {OPENAI_API_KEY}"},
                json={
                    "model": "gpt-4-turbo",
                    "messages": [{"role": "user", "content": prompt}]
                }
            )
            return response.json()['choices'][0]['message']['content'][:2000]
        except:
            return "Ошибка подключения к AI-сервису"
    
    def _handle_git(self, command: str) -> str:
        """Интеграция с GitHub"""
        # Реализованы базовые операции
        cmd = command.split()[0].lower()
        if cmd == "auth":
            return "🔑 Авторизация GitHub: https://github.com/login/oauth/authorize"
        elif cmd == "repo":
            return "https://github.com/username/repo"
        elif cmd == "pr":
            return "Список pull requests: ..."
        return "Доступные команды: auth, repo, pr, issues"
    
    def _set_reminder(self, params: str, user_id: int) -> str:
        """Установка напоминания"""
        try:
            time_str, text = re.match(r"(\d{2}:\d{2})\s(.+)", params).groups()
            self.reminders[user_id] = {"time": time_str, "text": text}
            return f"⏰ Напоминание установлено на {time_str}"
        except:
            return "Формат: /remind ЧЧ:ММ Текст"

# Пример использования (для реального бота нужна интеграция с фреймворком)
if __name__ == "__main__":
    bot = CodeMasterBot()
    print(bot.handle_message("/start", 123))
    print(bot.handle_message("/code Python quicksort", 123))
