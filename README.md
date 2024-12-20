# Работу выполнил Погребняк Степан Сиваселвамович

# Цель работы

![image](https://github.com/user-attachments/assets/70f84fbc-d8b0-4bce-8efd-5708915c370d)




```
#include <stdint.h>

#define GPIOA_BASE  0x48000000
#define RCC_BASE    0x40021000

#define GPIOA_MODER     (*(volatile uint32_t*)(GPIOA_BASE + 0x00))
#define GPIOA_ODR       (*(volatile uint32_t*)(GPIOA_BASE + 0x14))

#define RCC_AHBENR      (*(volatile uint32_t*)(RCC_BASE + 0x14))

// Битовые маски сегментов для цифр 0-9
uint8_t segment_map[] = {
    0b00111111, // 0
    0b00000110, // 1
    0b01011011, // 2
    0b01001111, // 3
    0b01100110, // 4
    0b01101101, // 5
    0b01111101, // 6
    0b00000111, // 7
    0b01111111, // 8
    0b01101111  // 9
};

// Инициализация GPIO для сегментов
void initGPIO() {
    // Включение тактирования GPIOA
    RCC_AHBENR |= (1 << 17); // Включение GPIOA
    
    // Настройка GPIOA в режим Output (для PA0-PA7)
    GPIOA_MODER &= ~(0xFFFF);  // Очистка режимов для PA0-PA7
    GPIOA_MODER |= 0x5555;     // Установка режимов Output для PA0-PA7
}

// Отображение цифры на семисегментном индикаторе
void displayDigit(uint8_t digit) {
    GPIOA_ODR = segment_map[digit]; // Установка состояния выходов
}

// Задержка (простейший цикл ожидания)
void simpleDelay(volatile uint32_t count) {
    while (count--);
}

// Вывод года на семисегментный индикатор (динамическая индикация)
void displayYear(uint16_t year) {
    uint8_t digits[4];
    digits[0] = year / 1000;        // Первая цифра
    digits[1] = (year / 100) % 10; // Вторая цифра
    digits[2] = (year / 10) % 10;  // Третья цифра
    digits[3] = year % 10;         // Четвёртая цифра
    
    for (int i = 0; i < 4; i++) {
        displayDigit(digits[i]); // Отображение цифры
        simpleDelay(500000);     // Примерная задержка
    }
}

int main() {
    initGPIO(); // Инициализация GPIO

    uint16_t current_year = 2024; // Текущий год
    uint16_t birth_year = 2000;  // Год рождения (пример)

    while (1) {
        displayYear(current_year); // Отображение текущего года
        simpleDelay(1000000);     // Задержка 1 секунда
        displayYear(birth_year);  // Отображение года рождения
        simpleDelay(1000000);     // Задержка 1 секунда
    }

    return 0;
}
```
