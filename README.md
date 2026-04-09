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
  tft.setAddrWindow(area->x1, area->y1, area->x2, area->y2);
  tft.pushColors((uint16_t*)color_p, (area->x2 - area->x1 + 1) * (area->y2 - area->y1 + 1), true);
  tft.endWrite();
  lv_disp_flush_ready(disp);
}
void init_touch() {
  // Add touch driver code here (XPT2046 or GT911)
}
void create_main_screen() {
  main_screen = lv_scr_act();
  lv_obj_set_style_bg_color(main_screen, lv_color_hex(0x1E1E1E), 0);
  lv_obj_t *title = lv_label_create(main_screen);
  lv_label_set_text(title, "📖 E-Book Reader");
  lv_obj_align(title, LV_ALIGN_TOP_MID, 0, 30);
  lv_obj_t *btn_library = lv_btn_create(main_screen);
  lv_obj_set_size(btn_library, 220, 70);
  lv_obj_align(btn_library, LV_ALIGN_TOP_MID, 0, 120);
  lv_obj_t *label = lv_label_create(btn_library);
  lv_label_set_text(label, "Open Library");
  lv_obj_center(label);
  lv_obj_add_event_cb(btn_library, library_btn_event, LV_EVENT_CLICKED, NULL);
}
void library_btn_event(lv_event_t *e) {
  // Open book list from SD card
}void init_sd_card() {
  if (!SD.begin()) {
    Serial.println("SD Card Mount Failed");
    return;
  }
  Serial.println("SD Card mounted successfully");
}
String read_file(const char *path) {
  File file = SD.open(path);
  if (!file) return "Failed to open file";
  String content = "";
  while (file.available()) content += (char)file.read();
  file.close();
  return content;
}
void show_text(const String &text) {
  reader_screen = lv_label_create(lv_scr_act());
  lv_label_set_long_mode(reader_screen, LV_LABEL_LONG_WRAP);
  lv_obj_set_width(reader_screen, SCREEN_WIDTH - 60);
  lv_obj_set_style_text_font(reader_screen, &lv_font_montserrat_20, 0);
  lv_label_set_text(reader_screen, text.c_str());
  lv_obj_align(reader_screen, LV_ALIGN_TOP_LEFT, 30, 30);
}
void lvgl_timer_callback() {
  lv_tick_inc(5);
}
void setup() {
  Serial.begin(115200);
  tft.begin();
  tft.setRotation(1);
  init_sd_card();
  setup_lvgl();
  init_touch();
  create_main_screen();
  lv_timer_create(lvgl_timer_callback, 5, NULL);
}
void loop() {
  lv_timer_handler();
  delay(5);
}
int text_font_size = 20;
lv_font_t *current_font = &lv_font_montserrat_20;
#define SCREEN_WIDTH 800
#define SCREEN_HEIGHT 480
TFT_eSPI tft = TFT_eSPI();
static lv_disp_draw_buf_t draw_buf;
static lv_color_t buf[SCREEN_WIDTH * 10];
lv_obj_t *main_screen, *reader_screen;
String current_book_path = "";
lv_obj_t *main_screen, *reader_screen;
String current_book_path = "";
int current_page = 0;
int text_font_size = 20;
bool dark_mode = true;
uint32_t bg_color = 0x1E1E1E;
uint32_t text_color = 0xFFFFFF;
struct Bookmark {
  String book_path;
  int page;
};
std::vector<Bookmark> bookmarks;
void display_flush(lv_disp_drv_t *disp, const lv_area_t *area, lv_color_t *color_p) {
  tft.startWrite();
  tft.setAddrWindow(area->x1, area->y1, area->x2, area->y2);
  tft.pushColors((uint16_t*)color_p, (area->x2-area->x1+1)*(area->y2-area->y1+1), true);
  tft.endWrite();
  lv_disp_flush_ready(disp);
}
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
}
void init_sd_card() {
  if (!SD.begin()) {
    Serial.println("SD Card Mount Failed");
    return;
  }
  Serial.println("SD Card mounted successfully");
}
String read_file(const char *path) {
  File file = SD.open(path);
  if (!file) return "Failed to open file";
  String content = "";
  while (file.available()) content += (char)file.read();
  file.close();
  return content;
}
void show_text(const String &text) {
  reader_screen = lv_label_create(lv_scr_act());
  lv_label_set_long_mode(reader_screen, LV_LABEL_LONG_WRAP);
  lv_obj_set_width(reader_screen, SCREEN_WIDTH - 60);
  lv_obj_set_style_text_font(reader_screen, &lv_font_montserrat_20, 0);
  lv_label_set_text(reader_screen, text.c_str());
  lv_obj_align(reader_screen, LV_ALIGN_TOP_LEFT, 30, 30);
}
void create_main_screen() {
  main_screen = lv_scr_act();
  lv_obj_set_style_bg_color(main_screen, lv_color_hex(bg_color), 0);
}
