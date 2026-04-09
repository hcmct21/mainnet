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
void setup_lvgl() {
  lv_init();
  lv_disp_draw_buf_init(&draw_buf, buf, NULL, SCREEN_WIDTH * 10);
  static lv_disp_drv_t disp_drv;
  lv_disp_drv_init(&disp_drv);
  disp_drv.hor_res = SCREEN_WIDTH;
  disp_drv.ver_res = SCREEN_HEIGHT;
  disp_drv.flush_cb = display_flush;
  disp_drv.draw_buf = &draw_buf;
  lv_disp_drv_register(&disp_drv);
}void display_flush(lv_disp_drv_t *disp, const lv_area_t *area, lv_color_t *color_p) {
  tft.startWrite();
