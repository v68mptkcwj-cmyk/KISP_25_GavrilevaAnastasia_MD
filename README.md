# Конспект с Expo

## Структура и организация процессов
**Этапы жизненного цикла:** Вся документация разделена по шагам работы над приложением. Раздел **Develop** содержит инструменты для написания кода и навигации, **Review** отвечает за совместное тестирование, **Deploy** описывает публикацию в магазины, а **Monitor** отслеживает работу сервисов в продакшене.

**Формат для ИИ-агентов:** Текст страниц адаптирован для чтения языковыми моделями (LLM). Если добавить расширение *.md* к любому URL-адресу документации, она откроется в виде чистого Markdown-файла.

## Возможности платформы и SDK
**Универсальный код:** Разработчик пишет один проект на JavaScript/TypeScript, который работает на Android, iOS и в веб-браузерах.

**Файловый роутинг:** Модуль **Expo Router** строит навигацию между экранами приложения автоматически на основе структуры папок и файлов проекта.

**Компоненты SDK:** Платформа предоставляет встроенные модули для доступа к функциям устройств. В качестве примеров в документации выделены модули Image (изображения), **Camera** (камера) и **Notifications** (уведомления).

**Тестирование без установки:** Инструмент **Expo Snack** позволяет запускать и проверять код приложения прямо в браузере без настройки локального окружения.

## Интеграция с искусственным интеллектом (AI)
**Инструменты для агентов:** В документацию добавлен отдельный блок по работе с ИИ. Он содержит руководства по **Expo Skills**, **MCP Server**, готовые наборы инструментов (**Agent toolkits**) и разделы для интеграции с **LLM.**

**Примеры реализации:** На сайте представлены готовые проекты для изучения, среди которых есть пример интеграции **API Routes + Open AI.**

## Консольные команды и автоматизация (CLI)
*npx create-expo-app@latest* — Команда для создания и инициализации нового чистого проекта.

*npx testflight* — Эксклюзивная команда для iOS, которая загружает готовую сборку приложения на платформу TestFlight.

*npx eas-cli deploy* — Инструмент для развертывания и публикации веб-версии вашего приложения.

**Сервис EAS Workflows:** Используется для настройки автоматического CI/CD цикла, позволяя собирать и выпускать релизы напрямую через GitHub.


# Конспект Expo tutorial

## Инициализация и настройка окружения (Introduction & Create your first app)

**Шаг 1. Создание проекта и развертывание Expo SDK**

Для развертывания базового шаблона на TypeScript и установки всех необходимых нативных модулей выполните в терминале следующие команды:

## Инициализация структуры проекта
```bash
npx create-expo-app@latest StickerSmash --template blank-typescript
cd StickerSmash
```

## Установка зависимостей Expo SDK для работы с медиа, жестами и графикой

```bash
npx expo install expo-image-picker react-native-gesture-handler react-native-reanimated react-native-view-shot expo-media-library expo-status-bar
```

**Шаг 2. Запуск локального сервера**

```bash
npx expo start
```

Перенесите разработку на физическое устройство: отсканируйте появившийся QR-код через мобильное приложение **Expo Go**.

## Архитектура файловой навигации (Add navigation)

Вся маршрутизация приложения строится на структуре каталогов внутри папки src/app/.

**Корневой файл компоновки** (src/app/_layout.tsx)

Инициализирует контекст обработки жестов, настраивает глобальный статус-бар (**Configure status bar**) и стек экранов.

```tsx
import { Stack } from 'expo-router';
import { StatusBar } from 'expo-status-bar';
import { GestureHandlerRootView } from 'react-native-gesture-handler';

export default function RootLayout() {
  return (
    <GestureHandlerRootView style={{ flex: 1 }}>
      <Stack>
        <Stack.Screen name="(tabs)" options={{ headerShown: false }} />
        <Stack.Screen name="+not-found" />
      </Stack>
      <StatusBar style="light" />
    </GestureHandlerRootView>
  );
}
```

**Компоновка нижнего меню** (src/app/(tabs)/_layout.tsx)

Управляет переключением между вкладками при помощи встроенного компонента Tabs.

```tsx
import { Tabs } from 'expo-router';
import Ionicons from '@expo/vector-icons/Ionicons';

export default function TabLayout() {
  return (
    <Tabs screenOptions={{ 
      tabBarActiveTintColor: '#ffd33d',
      headerStyle: { backgroundColor: '#25292e' },
      headerShadowVisible: false,
      headerTintColor: '#fff',
      tabBarStyle: { backgroundColor: '#25292e' },
    }}>
      <Tabs.Screen name="index" options={{
        title: 'Home',
        tabBarIcon: ({ color, focused }) => (
          <Ionicons name={focused ? 'home-sharp' : 'home-outline'} color={color} size={24} />
        ),
      }} />
      <Tabs.Screen name="about" options={{
        title: 'About',
        tabBarIcon: ({ color, focused }) => (
          <Ionicons name={focused ? 'information-circle-sharp' : 'information-circle-outline'} color={color} size={24} />
        ),
      }} />
    </Tabs>
  );
}
```

## Кастомные компоненты интерфейса (Create a modal)

**Универсальный компонент кнопки** (src/components/Button.tsx)

|Параметр (Prop)|Тип данных|Описание и поведение|
|---|---|---|
|`label`|`string`|Текст, отображаемый внутри кнопки|
|`theme`|`'primary' \| undefined`|Если выбран `primary` — кнопка становится белой с желтой рамкой|
|`onPress`|`() => void`|Функция-обработчик, срабатывающая при тапе по кнопке|

```tsx
import { StyleSheet, View, Pressable, Text } from 'react-native';
import FontAwesome from '@expo/vector-icons/FontAwesome';

type Props = { label: string; theme?: 'primary'; onPress?: () => void; };

export default function Button({ label, theme, onPress }: Props) {
  if (theme === 'primary') {
    return (
      <View style={[styles.buttonContainer, { borderWidth: 4, borderColor: '#ffd33d', borderRadius: 18 }]}>
        <Pressable style={[styles.button, { backgroundColor: '#fff' }]} onPress={onPress}>
          <FontAwesome name="picture-o" size={18} color="#25292e" style={styles.buttonIcon} />
          <Text style={[styles.buttonLabel, { color: '#25292e' }]}>{label}</Text>
        </Pressable>
      </View>
    );
  }

  return (
    <View style={styles.buttonContainer}>
      <Pressable style={styles.button} onPress={onPress}>
        <Text style={styles.buttonLabel}>{label}</Text>
      </Pressable>
    </View>
  );
}

const styles = StyleSheet.create({
  buttonContainer: { width: 320, height: 68, marginHorizontal: 20, alignItems: 'center', justifyContent: 'center', padding: 3 },
  button: { borderRadius: 10, width: '100%', height: '100%', alignItems: 'center', justifyContent: 'center', flexDirection: 'row' },
  buttonIcon: { paddingRight: 8 },
  buttonLabel: { color: '#fff', fontSize: 16 },
});
```

**Модальное окно выбора стикеров** (src/components/EmojiPicker.tsx)

```tsx
import { Modal, View, Text, Pressable, StyleSheet } from 'react-native';
import MaterialIcons from '@expo/vector-icons/MaterialIcons';

type Props = { isVisible: boolean; onClose: () => void; children: React.ReactNode; };

export default function EmojiPicker({ isVisible, onClose, children }: Props) {
  return (
    <Modal animationType="slide" transparent={true} visible={isVisible}>
      <View style={styles.modalContent}>
        <View style={styles.titleContainer}>
          <Text style={styles.title}>Choose a sticker</Text>
          <Pressable onPress={onClose}>
            <MaterialIcons name="close" color="#fff" size={22} />
          </Pressable>
        </View>
        {children}
      </View>
    </Modal>
  );
}

const styles = StyleSheet.create({
  modalContent: { height: '35%', width: '100%', backgroundColor: '#25292e', borderTopLeftRadius: 18, borderTopRightRadius: 18, position: 'absolute', bottom: 0 },
  titleContainer: { height: '16%', backgroundColor: '#464c55', borderTopLeftRadius: 18, borderTopRightRadius: 18, paddingHorizontal: 20, flexDirection: 'row', alignItems: 'center', justifyContent: 'space-between' },
  title: { color: '#fff', fontSize: 16 },
});
```

## Интеграция жестов и анимации (Add gestures)

**Поддерживаемые типы жестов в EmojiSticker.tsx**


|Жест|Метод библиотеки|Вызываемый эффект в приложении|
|---|---|---|
|**Двойной тап**|`Gesture.Tap().numberOfTaps(2)`|Увеличивает размер стикера в 2 раза или возвращает к исходному|
|**Перетаскивание (Pan)**|`Gesture.Pan()`|Свободно перемещает стикер по координатной сетке `X` и `Y`|


```tsx
import { Gesture, GestureDetector } from 'react-native-gesture-handler';
import Animated, { useAnimatedStyle, useSharedValue, withSpring } from 'react-native-reanimated';

type Props = { imageSize: number; stickerSource: any; };

export default function EmojiSticker({ imageSize, stickerSource }: Props) {
  const scaleImage = useSharedValue(imageSize);
  const translateX = useSharedValue(0);
  const translateY = useSharedValue(0);

  const doubleTap = Gesture.Tap().numberOfTaps(2).onStart(() => {
    scaleImage.value = scaleImage.value !== imageSize * 2 ? imageSize * 2 : imageSize;
  });

  const dragGesture = Gesture.Pan().onChange((event) => {
    translateX.value += event.changeX;
    translateY.value += event.changeY;
  });

  const containerStyle = useAnimatedStyle(() => ({
    transform: [{ translateX: translateX.value }, { translateY: translateY.value }],
  }));

  const imageStyle = useAnimatedStyle(() => ({
    width: withSpring(scaleImage.value),
    height: withSpring(scaleImage.value),
  }));

  return (
    <GestureDetector gesture={dragGesture}>
      <Animated.View style={[containerStyle, { top: -350, position: 'absolute' }]}>
        <GestureDetector gesture={doubleTap}>
          <Animated.Image source={stickerSource} resizeMode="contain" style={imageStyle} />
        </GestureDetector>
      </Animated.View>
    </GestureDetector>
  );
}
```


## Сборка экрана, галерея и скриншоты (Build a screen, Use an image picker, Take a screenshot & Handle platform differences)

Файл src/app/(tabs)/index.tsx связывает интерфейс, логику expo-image-picker и сохранение готового коллажа через react-native-view-shot. С помощью свойства Platform.OS распределяется логика работы приложения в зависимости от платформы запуска:

|Платформа запуска|Поведение функции сохранения `onSaveImageAsync`|
|---|---|
|**iOS / Android**|Запрашивается доступ, делается снимок экрана через `captureRef` и файл записывается в нативную галерею устройства|
|**Web (Браузер)**|Выводится предупреждение (нативный доступ к галереям мобильных ОС из браузера закрыт из соображений безопасности)|

```tsx
import { useState, useRef } from 'react';
import { View, StyleSheet, Platform, Image } from 'react-native';
import * as ImagePicker from 'expo-image-picker';
import * as MediaLibrary from 'expo-media-library';
import { captureRef } from 'react-native-view-shot';

import Button from '@/components/Button';
import EmojiPicker from '@/components/EmojiPicker';
import EmojiSticker from '@/components/EmojiSticker';

const PlaceholderImage = require('@/assets/images/background-image.png');

export default function Index() {
  const imageRef = useRef<View>(null);
  const [selectedImage, setSelectedImage] = useState<string | null>(null);
  const [showAppOptions, setShowAppOptions] = useState<boolean>(false);
  const [isModalVisible, setIsModalVisible] = useState<boolean>(false);
  const [pickedEmoji, setPickedEmoji] = useState<any>(null);
  const [status, requestPermission] = MediaLibrary.usePermissions();

  if (status === null) { requestPermission(); }

  const pickImageAsync = async () => {
    let result = await ImagePicker.launchImageLibraryAsync({
      allowsEditing: true,
      quality: 1,
    });

    if (!result.canceled) {
      setSelectedImage(result.assets.uri);
      setShowAppOptions(true);
    }
  };

  const onSaveImageAsync = async () => {
    if (Platform.OS !== 'web') {
      try {
        const localUri = await captureRef(imageRef, { height: 440, quality: 1 });
        await MediaLibrary.saveToLibraryAsync(localUri);
        if (localUri) alert('Saved successfully!');
      } catch (e) {
        console.log(e);
      }
    } else {
      alert('Saving screenshots directly to gallery is not supported on Web.');
    }
  };

  return (
    <View style={styles.container}>
      <View ref={imageRef} collapsable={false} style={styles.imageContainer}>
        <Image source={selectedImage ? { uri: selectedImage } : PlaceholderImage} style={styles.image} />
        {pickedEmoji && <EmojiSticker imageSize={40} stickerSource={pickedEmoji} />}
      </View>

      {showAppOptions ? (
        <View style={styles.optionsContainer}>
          <Button label="Choose a sticker" onPress={() => setIsModalVisible(true)} />
          <Button theme="primary" label="Save Photo" onPress={onSaveImageAsync} />
        </View>
      ) : (
        <View style={styles.footerContainer}>
          <Button theme="primary" label="Choose a photo" onPress={pickImageAsync} />
          <Button label="Use this photo" onPress={() => setShowAppOptions(true)} />
        </View>
      )}

      <EmojiPicker isVisible={isModalVisible} onClose={() => setIsModalVisible(false)}>
        {/* Сюда импортируется FlatList со списком доступных смайликов */}
      </EmojiPicker>
    </View>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, backgroundColor: '#25292e', alignItems: 'center' },
  imageContainer: { flex: 1, paddingTop: 58 },
  image: { width: 320, height: 440, borderRadius: 18 },
  footerContainer: { flex: 1 / 3, alignItems: 'center' },
  optionsContainer: { flex: 1 / 3, justifyContent: 'center', alignItems: 'center' },
});
```

## Системный манифест приложения (Configure status bar, splash screen and app icon)

Глобальный файл app.json содержит метаданные проекта.

```json
{
  "expo": {
    "name": "StickerSmash",
    "slug": "StickerSmash",
    "version": "1.0.0",
    "orientation": "portrait",
    "icon": "./assets/images/icon.png",
    "userInterfaceStyle": "dark",
    "splash": {
      "image": "./assets/images/splash-screen.png",
      "resizeMode": "contain",
      "backgroundColor": "#25292e"
    },
    "ios": { 
      "supportsTablet": true 
    },
    "android": {
      "adaptiveIcon": {
        "foregroundImage": "./assets/images/adaptive-icon.png",
        "backgroundColor": "#25292e"
      }
    },
    "web": { 
      "favicon": "./assets/images/favicon.png" 
    }
  }
}
```

## Дополнительные ресурсы (Learning resources)

Для углубления в экосистему Expo изучите следующие направления:

**EAS Build** —  инструмент облачной компиляции нативных установочных пакетов под iOS (.ipa) и Android (.apk).

**Разделение платформенного кода** — углубленная работа с расширениями файлов для гибкой кастомизации под веб-интерфейсы (.web.tsx).