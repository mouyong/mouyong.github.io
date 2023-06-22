# 【代码片段】一个简单的 Gateway Events 事件处理代码片段分享

文章编写于 2022年04月14日

```php
<?php

abstract class AbstractEvent
{
    protected $eventType;

    public function handle($connection, array $event)
    {
        if ($this->eventType !== $event['event_type']) {
            return;
        }

        $this->handleEvent($connection, $event);
    }
}

class LoginEvent extends AbstractEvent
{
    protected $eventType = 'user.login';

    public function handleEvent($connection, $event)
    {
        // handle user login event
    }
}

class JoinRoomEvent extends AbstractEvent
{
    protected $eventType = 'room.join';

    public function handleEvent($connection, $event)
    {
        // handle user join room event
    }
}

class Factory
{
    // protected static $eventType = [
    //     'login' => Login::class,
    //     'room.join' => RoomJoin::class,
    // ];

    // public static function make(array $data)
    // {
    //     $className = static::$eventTypeMap[$data['event_type']];

    //     $class = new $className();

    //     return $class;
    // }

    public static function make(array $event)
    {
        $eventTypePart = explode('.', $event['event_type']);

        $ucfirstEventTypePart = array_map('ucfirst', $eventTypePart);

        $className = implode('', $ucfirstEventTypePart);

        $class = "\\App\\Services\\{$className}";

        // validate class exists

        return new $class;
    }
}



class Events
{
    // protected $eventType = [
    //     'login' => Login::class,
    //     'room.join' => RoomJoin::class,
    // ];

    // public function onMessage($connection, string $event)
    // {
    //     $data = json_decode($event, true) ?? [];

    //     // event validate


    //     $className = $this->eventTypeMap[$data['event_type']];

    //     $class = new $className();

    //     $class->handle($connection, $data);
    // }

    // public function onMessage($connection, string $event)
    // {
    //     $data = json_decode($event, true) ?? [];

    //     // event validate

    //     foreach ($this->eventType as $eventName => $eventHandle) {
    //         $handle = new $eventHandle();

    //         $handle->handle($connection, $data);
    //     }
    // }

    public function onMessage($connection, string $event)
    {
        $data = json_decode($event, true) ?? [];

        // event validate

        $eventHandle = Factory::make($data);

        $eventHandle->handle($connection, $data);
    }
}
```