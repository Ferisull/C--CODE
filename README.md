#pragma once
#include "memory"
#include <cassert>

namespace it_step
{
    template<typename T>
    class dynamic_array
    {
    public:
        dynamic_array();
        dynamic_array(size_t size);
        dynamic_array(size_t size, const T& defaultValue);

        dynamic_array(const dynamic_array<T>& right);
        dynamic_array(dynamic_array<T>&& right);

        dynamic_array<T>& operator=(const dynamic_array<T>& right);
        dynamic_array<T>& operator=(dynamic_array<T>&& right);

        ~dynamic_array() = default;

        T& at(size_t idx);
        const T& at(size_t idx) const;

        T& operator[](size_t idx);
        const T& operator[](size_t idx) const;

        size_t size();
        size_t capacity();

        void push_back(T&& value);
		void push_back(const T& value);

        T pop_back();
        void erase(T* value);
        void erase(size_t idx);

		void clear();
		void resize(size_t newSize);
		void reserve(size_t newCapacity);

        T* begin();
        const T* begin() const;
        T* end();
        const T* end() const;

    private:
        std::unique_ptr<T[]> _data;
        size_t _size;
        size_t _capacity;
    };

    template <typename T>
    dynamic_array<T>::dynamic_array() : _data(nullptr), _size(0), _capacity(0) {}

    template <typename T>
    dynamic_array<T>::dynamic_array(const dynamic_array<T>& right)
    {
        _size = right._size;
        _data.reset(new T[_capacity = _size * 2]);

        for (int i = 0; i < _size; i++)

            _data[i] = right._size;
    }

    template <typename T>
    dynamic_array<T>::dynamic_array(dynamic_array<T>&& right)
    {
        _data = std::move(right._data);
        _size = std::move(right._size);
        _capacity = std::move(right._capacity);

        right._data = nullptr;
        right._size = 0;
        right._capacity = 0;
    }

    template <typename T>
    dynamic_array<T>& dynamic_array<T>::operator=(const dynamic_array<T>& right)
    {
        if (this != &right)
        {
            _size = right._size;
            _data.reset(new T[_capacity = _size * 2]);

            for (int i = 0; i < _size; i++)

                _data[i] = right._size;
        }
        return *this;
    }

    template <typename T>
    dynamic_array<T>& dynamic_array<T>::operator=(dynamic_array<T>&& right)
    {
        if (this != &right)
        {
            _data = std::move(right._data);
            _size = std::move(right._size);
            _capacity = std::move(right._capacity);

            right._data = nullptr;
            right._size = 0;
            right._capacity = 0;
        }
        return *this;
    }

    template <typename T>
    dynamic_array<T>::dynamic_array(size_t size)
    {
        _size = size;
        _data.reset(new T[_capacity = _size * 2]);
    }

    template <typename T>
    dynamic_array<T>::dynamic_array(size_t size, const T& defaultValue)
    {
        _size = size;
        _data.reset(new T[_capacity = _size * 2]);

        for (int i = 0; i < _size; i++)

            _data[i] = defaultValue;
    }

    template <typename T>
    T& dynamic_array<T>::operator[](size_t idx)
    {
        if (_size <= idx) 
        {
            assert(false);
            throw std::out_of_range("Out of range");
        }
        return _data[idx];
    }

    template <typename T>
    const T& dynamic_array<T>::operator[](size_t idx) const
    {
        if (_size <= idx)
        {
            assert(false);
            throw std::out_of_range("Out of range");
        }
        return _data[idx];
    }

    template <typename T>
    T& dynamic_array<T>::at(size_t idx)
    {
        if (_size <= idx)
        {
            assert(false);
            throw std::out_of_range("Out of range");
        }
        return _data[idx];
    }

    template <typename T>
    const T& dynamic_array<T>::at(size_t idx) const
    {
        if (_size <= idx)
        {
            assert(false);
            throw std::out_of_range("Out of range");
        }
        return _data[idx];
    }

    template <typename T>
    size_t dynamic_array<T>::size()
    {
        return _size;
    }

    template <typename T>
    size_t dynamic_array<T>::capacity()
    {
        return _capacity;
    }

    template <typename T>
    void dynamic_array<T>::push_back(T&& value)
    {
        if (_capacity == _size)
        {
            T* new_data = new T[_capacity = (_size + 1) * 2];
            if (_size > 0)
            
                memcpy(new_data, _data.release(), (_size + 1) * sizeof(T));
            
            _data.reset(new_data);
            new_data = nullptr;
        }
        _data[_size++] = std::move(value);
    }

    template <typename T>
    void dynamic_array<T>::push_back(const T& value)
    {
        if (_capacity == _size)
        {
            T* new_data = new T[_capacity = _size * 2];
        	if(_size > 0)

				memcpy(new_data, _data.release(), (_size + 1) * sizeof(T));
        	
            _data.reset(new_data);
            new_data = nullptr;
        }
        _data[_size++] = value;
    }

    template <typename T>
    T dynamic_array<T>::pop_back()
    {
        if (!_data || _size - 1 < 0)
        {
            assert(false);
            throw std::out_of_range("Out of range");
        }
        T element = _data[_size-- - 1];
        T* new_data = new T[_capacity];

        memcpy(new_data, _data.release(), _size * sizeof(T));
        _data.reset(new_data);
        new_data = nullptr;

        return element;
    }

    template <typename T>
    void dynamic_array<T>::erase(T* value)
    {
        if (!_data || _size - 1 < 0)
        {
            assert(false);
            throw std::out_of_range("Out of range");
        }
        for (int i = 0; i < _size; i++)

            if (_data[i] == *value)

                erase(size_t(i));
    }

    template <typename T>
    void dynamic_array<T>::erase(size_t idx)
    {
        if (!_data || _size - 1 < 0)
        {
            assert(false);
            throw std::out_of_range("Out of range");
        }
		for(int i = idx;i < _size;i++)
		
            _data[i] = _data[i + 1];
		
        --_size;
    }

    template <typename T>
    void dynamic_array<T>::clear()
    {
        _data.reset();
        _size = 0;
        _capacity = 0;
    }

    template <typename T>
    void dynamic_array<T>::resize(size_t newSize)
    {
        if (!_data)
        {
            assert(false);
            throw std::out_of_range("Out of range");
        }
        if (newSize < _size) 
        {
            for (int i = newSize; i < _size; i++)

                _data[i] = static_cast<T>(0);
            
            _size = newSize;
        }
        else
        {
            if (newSize > _capacity)

                reserve(newSize);
        }
    }

    template <typename T>
    void dynamic_array<T>::reserve(size_t newCapacity)
    {
        if(!_data)
        {
            _capacity = newCapacity;
            _data.reset(new T[_capacity]);
        }
        else
        {
            T* new_data = new T[_capacity = newCapacity];
            _size = newCapacity >= _capacity ? _size : newCapacity;
            memcpy(new_data, _data.release(), _capacity * sizeof(T));
            _data.reset(new_data);
            new_data = nullptr;
        }
    }

    template <typename T>
    T* dynamic_array<T>::begin()
    {
        return _data.get();
    }

    template <typename T>
    const T* dynamic_array<T>::begin() const
    {
        return _data.get();
    }

    template <typename T>
    T* dynamic_array<T>::end()
    {
        return (_data.get() + _size - 1);
    }

    template <typename T>
    const T* dynamic_array<T>::end() const
    {
        return (_data.get() + _size - 1);
    }
	
}












