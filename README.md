#include <Arduino.h>
#include <LVGL.h>
#include <TFT_eSPI.h>
#include <SD.h>
#include <FS.h>#define SCREEN_WIDTH 800
#define SCREEN_HEIGHT 480
TFT_eSPI tft = TFT_eSPI();
static lv_disp_draw_buf_t draw_buf;
static lv_color_t buf[SCREEN_WIDTH * 10];
lv_obj_t *main_screen, *book_list, *reader_screen;
String current_book_path = "";
int current_page = 0;
