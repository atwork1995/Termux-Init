# termux_init.sh
Init termux when first install,but don't achieve
For a little more convient,use ZeroTermux.dev,but non-Chinese support bad
**Default no SHIFT,and can't copy better to Android,f**k**
(no way use shorcut to Android,long press to copy hard to got long code)

auto dark mode script is in it,# says unachieve problem,who like and achieve it.

preview:
``#!/data/data/com.termux/files/usr/bin/bash
printf "termus is a app that use Android Linux kernel to run Linux app\n\n"
printf "only two ways to achieve auto dark mode:\n"
printf "1.unpack termux.apk and add forceDarkAllowed and others\n"
printf "2.replace file like Termux:Styling by script or Tasker App\n"
#request phone storage
termux-setup-storage 
printf "If show \"permisson denied\", re-open right"

#choose source gui
termux-change-repo

printf "replace for \"normal\" micro,no -y for your choice\n"
pkg uninstall nano
pkg install micro

#maybe I should follow modern?
printf "install maumal(no man pre-),tldr rust,too long didn't read\n"
pkg install tealdeer
export TLDR_SOURCE="https://cdn.jsdelivr.net/gh/tldr-pages/tldr@main/pages"
tldr --update

#install auto dark to system
if [ ! -f "$PREFIX/etc/termux-theme.conf" ]; then
cat <<EOF > "$PREFIX/etc/termux-theme.conf"
SCHEDULE_SUNRISE=07:00
SCHEDULE_SUNSET=18:00
USE_SCHEDULE_TIME=false
GEO_LAT=0.0
GEO_LNG=0.0
USE_GEO_TIME=true
SUNRISE_COLOR=
SUNRISE_FONT=
SUNSET_COLOR=
SUNSET_FONT=
EOF
fi
if [ ! -f "$PREFIX/bin/termux-theme.sh" ]; then
cat <<EOF > "$PREFIX/bin/termux-theme.sh" 
#!/data/data/com.termux/files/usr/bin/bash
set -eu  # return error code and undefined exit

. "$PREFIX/etc/termux-theme.conf"

printf "run $PREFIX/share/termux-theme/switch.sh to mamually switch theme\n"
while true; do
readonly RESOURCE="$PREFIX/share/termux-theme/"
if [ "${USE_SCHEDULE_TIME:-}" = "true" ]; then
  if [[ "$(date +"%H:%M")" > "$SCHEDULE_SUNRISE" ]]; then
    $RESOURCE/switch.sh "$RESOURCE/themes/$SUNRISE_COLOR.properties"  "$RESOURCE/theme/$SUNRISE_FONT.ttf"; fi
  if [[ "$(date +"%H:%M")" > "$SCHEDULE_SUNSET" ]]; then
    $RESOURCE/switch.sh "$RESOURCE/themes/$SUNSET_COLOR.properties" "$RESOURCE/theme/$SUNSET_FONT.ttf"; fi
fi

if [ "${USE_GEO_TIME:-}" = "true" ]; then
  response=$(curl "https://api.sunrise-sunset.org/json?lat=${GEO_LAT}&lng=${GEO_LNG}&formatted=0")
  sunrise_hm=$(
    printf "%s\n" "$response" \
    | grep -o '"sunrise":"[^"]*"' \
    | sed 's/"sunrise":"//' \
    | sed 's/"//' \
    | xargs -I{} date -d {} +"%H:%M"
  )

  sunset_hm=$(
    printf "%s\n" "$response" \
    | grep -o '"sunset":"[^"]*"' \
    | sed 's/"sunset":"//' \
    | sed 's/"//' \
    | xargs -I{} date -d {} +"%H:%M"
  )

  if [[ "$(date +"%H:%M")" > "$sunrise_hm" ]]; then
    $RESOURCE/switch.sh "$RESOURCE/themes/$SUNRISE_COLOR" "$RESOURCE/themes/$SUNRISE_FONT"; fi
  if [[ "$(date +"%H:%M")" > "$sunset_hm" ]]; then
    $RESOURCE/switch.sh "$RESOURCE/themes/$SUNSET_COLOR.properties" "$RESOURCE/themes/$SUNSET_FONT.ttf"; fi
fi

sleep 300
done

#{
#  "results": {
#    "sunrise": "2025-12-25T21:00:00+00:00",
#    "sunset": "2025-12-26T07:00:00+00:00"
#  },
#  "status": "OK"
#}
EOF
fi
if [ ! -f "$PREFIX/share/termux-theme/switch.sh"]
cat <<EOF > "$PREFIX/share/termux-theme/switch.sh"
if [ $# -ne 2 ]; then
  printf "parameter error\n"
   #鍙傛暟鏁伴噺鎬庝箞璇�?
   #rm涓轰粈涔堣鍔�-f
   #鎬庝箞澶勭悊鏃跺尯闂
   #闄や簡涓婅堪鍑犱釜闂浠ュ強浣犺鐨勯棶棰樻垜閮芥敼浜嗕箣澶栵紝杩樻湁浠€涔堥棶棰�?
   #termux鐨剆hift鍜宲aste閿湪termux.properties閲屽姞鍚嶅瓧灏辫浜�?閿殑浣嶇疆鎬庝箞鍒ゆ柇鐨�?
   #micro鐨勬浛鎹㈡槸鍝釜?浼犵粺瑙ｅ喅鍙橀噺鏀瑰悕鏄笉鏄氨鐢ㄨ繖涓�?
   # problem there,Chinese,click origin file to get
  printf "Usage:switch.sh [COLOR] [FONT] (file path,properties,ttf)"
  exit 1
fi

COLOR="$1"; FONT="$2"
rm -f ~/.termux/color.properties
cp $COLOR ~/.termux/color.properties
rm -f ~/.termux/font.ttf
cp $FONT ~/.termux/font.ttf
EOF
fi
chmod +x '$PREFIX/bin/termux-theme.sh'


while true; do
  printf  "Do you want get or also get anything else?\n"
  printf "1) adb\n"
  printf "9) Exit\n"
  read choice

  case $choice in
      1) pkg install android-tools
         adb pair
         adb connect
         printf "read above,connect other_deviceip" ;;
      9) printf "notice welcome information to learn somthing"
         break ;;
  esac
done``
