// Patlayan bomba sınıfı
class Bomb {
public:
    // Bomba oluşturulduğunda başlangıç değerleri
    Bomb(float x, float y, float timer, float radius) {
        this->x = x; // Bombanın x koordinatı
        this->y = y; // Bombanın y koordinatı
        this->timer = timer; // Bombanın patlama süresi (saniye)
        this->radius = radius; // Bombanın patlama yarıçapı (birim)
        this->exploded = false; // Bombanın patlayıp patlamadığını tutan değişken
        this->shape = new Cylinder(x, y, radius, 10); // Bombanın silindir şeklinde görünümü
        this->effect = new ParticleSystem(x, y, radius, 100); // Bombanın alev ve duman efekti
    }

    // Bombanın güncellenmesi
    void update(float dt) {
        // Bomba patlamadıysa
        if (!exploded) {
            // Bombanın süresini azalt
            timer -= dt;
            // Bombanın süresi bittiyse
            if (timer <= 0) {
                // Bombayı patlat
                explode();
            }
        }
        // Bombanın efektini güncelle
        effect->update(dt);
    }

    // Bombanın patlaması
    void explode() {
        // Bombanın patladığını belirle
        exploded = true;
        // Bombanın etrafındaki nesneleri bul
        vector<GameObject*> objects = find_objects_in_radius(x, y, radius);
        // Bombanın etkilediği nesneler için
        for (GameObject* obj : objects) {
            // Nesneye hasar ver
            obj->damage(radius - distance(x, y, obj->x, obj->y));
            // Nesneyi it
            obj->push(x, y, radius);
        }
        // Bombanın efektini başlat
        effect->start();
    }

    // Bombanın çizilmesi
    void draw() {
        // Bomba patlamadıysa
        if (!exploded) {
            // Bombanın şeklini çiz
            shape->draw();
        }
        // Bombanın efektini çiz
        effect->draw();
    }

private:
    float x, y; // Bombanın konumu
    float timer; // Bombanın süresi
    float radius; // Bombanın yarıçapı
    bool exploded; // Bombanın patlama durumu
    Cylinder* shape; // Bombanın şekli
    ParticleSystem* effect; // Bombanın efekti
};
// Bombanın etrafındaki nesneleri bulan fonksiyon
vector<GameObject*> find_objects_in_radius(float x, float y, float radius) {
    // Boş bir vektör oluştur
    vector<GameObject*> objects;
    // Oyundaki tüm nesneler için
    for (GameObject* obj : game_objects) {
        // Nesnenin bombaya olan uzaklığını hesapla
        float dist = distance(x, y, obj->x, obj->y);
        // Uzaklık yarıçaptan küçük veya eşitse
        if (dist <= radius) {
            // Nesneyi vektöre ekle
            objects.push_back(obj);
        }
    }
    // Vektörü döndür
    return objects;
}

// İki nokta arasındaki uzaklığı hesaplayan fonksiyon
float distance(float x1, float y1, float x2, float y2) {
    // Pisagor teoremini kullan
    return sqrt((x2 - x1) * (x2 - x1) + (y2 - y1) * (y2 - y1));
}

// Bir nesneye hasar veren fonksiyon
void damage(float amount) {
    // Nesnenin canını azalt
    hp -= amount;
    // Nesnenin canı sıfıra eşit veya küçükse
    if (hp <= 0) {
        // Nesneyi yok et
        destroy();
    }
}

// Bir nesneyi iten fonksiyon
void push(float x, float y, float radius) {
    // Nesnenin bombaya olan açısını hesapla
    float angle = atan2(y - this->y, x - this->x);
    // Nesnenin bombaya olan uzaklığını hesapla
    float dist = distance(x, y, this->x, this->y);
    // Nesnenin hızını açı ve uzaklığa göre belirle
    vx = cos(angle) * (radius - dist);
    vy = sin(angle) * (radius - dist);
}
